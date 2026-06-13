# Data Center Capacity Planning & Energy Management

![Capacity Planning](https://img.shields.io/badge/Domain-Capacity%20Planning-blue)
![Energy Management](https://img.shields.io/badge/Focus-Energy%20Efficiency-green)
![PUE](https://img.shields.io/badge/Target%20PUE-Below%201.5-brightgreen)
![Schneider DCCA](https://img.shields.io/badge/Cert-Schneider%20DCCA-orange)
![Uptime Institute](https://img.shields.io/badge/Standard-Uptime%20Institute-blue)
![ASHRAE](https://img.shields.io/badge/Standard-ASHRAE%20TC%209.9-green)

---

## Project Overview

This project documents a comprehensive data center capacity planning and energy management framework — covering power capacity analysis, cooling headroom tracking, space utilisation, PUE optimisation, energy efficiency strategies, and multi-year growth planning aligned with Uptime Institute and ASHRAE standards.

Built to demonstrate operational readiness for:
- Data Center Operations Engineer roles
- Critical Infrastructure Engineer roles
- Data Center Capacity Planner roles
- Facilities Engineer roles

---

## Repository Structure

dc-capacity-planning-energy-management/

│

├── docs/

│   └── capacity-overview.md               # Framework overview

│

├── power-capacity/

│   ├── current-load-analysis.md            # Current power analysis

│   ├── headroom-calculation.md            # Power headroom

│   ├── growth-projection.md               # Growth planning

│   └── redundancy-planning.md            # Redundancy design

│

├── cooling-capacity/

│   ├── cooling-load-analysis.md            # Cooling analysis

│   ├── headroom-tracking.md               # Cooling headroom

│   └── thermal-mapping.md                # Temperature mapping

│

├── space-capacity/

│   ├── rack-space-utilisation.md           # Rack utilisation

│   ├── floor-load-planning.md            # Floor load limits

│   └── cable-pathway-planning.md         # Cable management

│

├── energy-management/

│   ├── pue-tracking.md                    # PUE measurement

│   ├── energy-efficiency-strategies.md    # Efficiency measures

│   └── carbon-footprint.md              # Carbon tracking

│

├── reporting/

│   ├── capacity-dashboard.md              # Dashboard config

│   ├── monthly-capacity-report.md        # Monthly template

│   └── executive-capacity-summary.md     # Executive summary

│

├── planning/

│   ├── 3-year-capacity-plan.md            # Long term plan

│   └── refresh-cycle-planning.md         # Hardware refresh

│

├── runbooks/

│   ├── capacity-breach-response.md        # Breach response

│   └── emergency-power-reduction.md      # Emergency procedure

│

└── images/

└── README.md                          # Diagrams and charts
---

##  Power Capacity Analysis

### Current Load Assessment

```python
# power_capacity_analyzer.py
import json
from dataclasses import dataclass
from typing import List

@dataclass
class RackPowerData:
    rack_id: str
    location: str
    nameplate_capacity_kw: float
    measured_load_kw: float
    redundancy_config: str  # 2N, N+1, N

def analyze_power_capacity(racks: List[RackPowerData]) -> dict:
    """
    Analyze current power capacity and headroom
    across all racks in the data center
    """
    
    total_capacity = sum(r.nameplate_capacity_kw for r in racks)
    total_load = sum(r.measured_load_kw for r in racks)
    utilization = (total_load / total_capacity) * 100
    headroom = total_capacity - total_load
    
    # Design headroom — target below 70% utilization
    design_headroom = (total_capacity * 0.70) - total_load
    
    # Identify high utilization racks
    high_util_racks = [
        r for r in racks
        if (r.measured_load_kw / r.nameplate_capacity_kw) > 0.75
    ]
    
    return {
        'total_capacity_kw': round(total_capacity, 2),
        'total_load_kw': round(total_load, 2),
        'utilization_percent': round(utilization, 2),
        'physical_headroom_kw': round(headroom, 2),
        'design_headroom_kw': round(design_headroom, 2),
        'high_utilization_racks': len(high_util_racks),
        'status': get_capacity_status(utilization),
        'recommendation': get_recommendation(utilization, design_headroom)
    }

def get_capacity_status(utilization: float) -> str:
    if utilization < 60:
        return 'GREEN — Healthy headroom available'
    elif utilization < 75:
        return 'YELLOW — Monitor closely, plan expansion'
    elif utilization < 85:
        return 'AMBER — Expansion required within 6 months'
    else:
        return 'RED — Critical — Immediate action required'

def get_recommendation(utilization: float, headroom: float) -> str:
    if utilization > 80:
        return 'Immediate capacity expansion required — contact procurement'
    elif utilization > 70:
        return 'Begin capacity planning process — 6 month lead time'
    elif headroom < 20:
        return 'Low design headroom — review growth projections'
    else:
        return 'Capacity healthy — review quarterly'

# Example data
sample_racks = [
    RackPowerData('R01', 'Row-A', 20.0, 14.5, '2N'),
    RackPowerData('R02', 'Row-A', 20.0, 16.8, '2N'),
    RackPowerData('R03', 'Row-B', 20.0, 12.3, 'N+1'),
    RackPowerData('R04', 'Row-B', 20.0, 18.1, 'N+1'),
]

result = analyze_power_capacity(sample_racks)
print(json.dumps(result, indent=2))
```

---

##  PUE Tracking & Optimisation

### PUE Calculator

```python
# pue_calculator.py
from datetime import datetime, timedelta
import statistics

def calculate_pue(
    total_facility_power_kw: float,
    it_equipment_power_kw: float
) -> dict:
    """
    Calculate Power Usage Effectiveness (PUE)
    PUE = Total Facility Power / IT Equipment Power
    Target: Below 1.5 | World Class: Below 1.2
    """
    
    pue = total_facility_power_kw / it_equipment_power_kw
    overhead_power = total_facility_power_kw - it_equipment_power_kw
    overhead_percent = (overhead_power / total_facility_power_kw) * 100
    
    return {
        'pue': round(pue, 3),
        'total_facility_power_kw': total_facility_power_kw,
        'it_equipment_power_kw': it_equipment_power_kw,
        'overhead_power_kw': round(overhead_power, 2),
        'overhead_percent': round(overhead_percent, 2),
        'rating': get_pue_rating(pue),
        'improvement_target': calculate_improvement_target(pue, it_equipment_power_kw)
    }

def get_pue_rating(pue: float) -> str:
    if pue < 1.2:
        return 'World Class — Exceptional efficiency'
    elif pue < 1.4:
        return 'Excellent — Above industry average'
    elif pue < 1.6:
        return 'Good — Meeting industry standards'
    elif pue < 2.0:
        return 'Average — Improvement opportunities exist'
    else:
        return 'Poor — Significant improvement required'

def calculate_improvement_target(pue: float, it_power: float) -> dict:
    target_pue = 1.4
    current_total = pue * it_power
    target_total = target_pue * it_power
    potential_saving_kw = current_total - target_total
    annual_saving_kwh = potential_saving_kw * 8760
    annual_cost_saving = annual_saving_kwh * 0.10  # $0.10/kWh estimate
    
    return {
        'target_pue': target_pue,
        'potential_power_saving_kw': round(potential_saving_kw, 2),
        'annual_energy_saving_kwh': round(annual_saving_kwh, 0),
        'estimated_annual_cost_saving_usd': round(annual_cost_saving, 2)
    }

# Example usage
result = calculate_pue(
    total_facility_power_kw=175.0,
    it_equipment_power_kw=120.0
)
print(json.dumps(result, indent=2))
```

---

##  3-Year Capacity Plan

### Growth Projection Model

| Year | IT Load (kW) | Cooling Required | Rack Count | Power Headroom |
|---|---|---|---|---|
| Current (2026) | 120 kW | 144 kW | 25 racks | 45% |
| Year 1 (2027) | 150 kW | 180 kW | 32 racks | 32% |
| Year 2 (2028) | 185 kW | 222 kW | 38 racks | 18% |
| Year 3 (2029) | 225 kW | 270 kW | 46 racks | Expansion required |

### Capacity Expansion Triggers

| Metric | Trigger Threshold | Lead Time | Action |
|---|---|---|---|
| Power utilization | Above 70% | 12 months | Begin procurement |
| Cooling utilization | Above 75% | 9 months | Engage vendor |
| Rack space | Above 80% | 6 months | Plan expansion |
| Network ports | Above 80% | 3 months | Order switches |

---

## Monthly Capacity Report Template

```markdown
## Data Center Monthly Capacity Report

**Month:** _______________
**Prepared By:** _______________
**Date:** _______________

---

### Executive Summary
Overall data center capacity utilization: ___%
PUE this month: ___
Power headroom: ___ kW (___ %)
Cooling headroom: ___ kW (___ %)
Rack space available: ___ U (___ %)

---

### Power Capacity

| Metric | Current | Last Month | Trend | Status |
|---|---|---|---|---|
| Total Load | | | ↑/↓/→ | 🟢/🟡/🔴 |
| Utilization % | | | | |
| Headroom kW | | | | |
| PUE | | | | |

### Cooling Capacity

| Metric | Current | Last Month | Trend | Status |
|---|---|---|---|---|
| Cooling Load | | | ↑/↓/→ | 🟢/🟡/🔴 |
| Utilization % | | | | |
| Headroom kW | | | | |
| Avg Inlet Temp | | | | |

### Space Capacity

| Metric | Current | Last Month | Trend |
|---|---|---|---|
| Total Racks | | | |
| Occupied Racks | | | ↑/↓/→ |
| Available U | | | |
| Utilization % | | | |

### New Deployments This Month
| Date | Asset | Rack | Power | Cooling |
|---|---|---|---|---|
| | | | | |

### Capacity Alerts & Actions
| Alert | Status | Action | Owner | Due |
|---|---|---|---|---|
| | | | | |

### 3-Month Forecast
Based on current growth rate of ___% per month:
- Power utilization in 3 months: ___%
- Cooling utilization in 3 months: ___%
- Rack space in 3 months: ___%
- Recommended actions: _______________
```

---

## Capacity Breach Response Runbook

```markdown
### Trigger Conditions
- Power utilization exceeds 80%
- Cooling utilization exceeds 85%
- Rack space below 10% available
- PUE exceeds 2.0

### Immediate Response (0–30 minutes)
1. Acknowledge capacity alert
2. Create P2 incident ticket
3. Verify reading is accurate — not sensor error
4. Identify which systems are driving the spike
5. Notify Data Center Manager

### Short-Term Actions (Same day)
1. Identify any non-critical loads that can be reduced
2. Check for any zombie or unused servers
3. Verify all power management settings optimal
4. Review recent deployments for oversized allocations
5. Check CRAC unit operation — all units running?

### Emergency Power Reduction Procedure
If power utilization above 90%:
1. Identify lowest priority systems
2. Get approval from management
3. Gracefully shut down non-critical development systems
4. Verify power reading drops below 85%
5. Notify all affected teams
6. Document all systems shutdown with timestamps

### Long-Term Actions (Within 30 days)
1. Commission capacity expansion study
2. Engage procurement for additional hardware
3. Review and update 3-year capacity plan
4. Present findings to management with options
5. Get budget approval for expansion

### Documentation
- Record all readings during capacity event
- Document all actions taken
- Update capacity planning spreadsheet
- Schedule capacity review meeting
```

---

## Standards & Frameworks Referenced

- **Uptime Institute** — Tier operational sustainability
- **The Green Grid** — PUE and WUE measurement
- **ASHRAE TC 9.9** — Thermal guidelines and capacity
- **ANSI/TIA-942** — Data center infrastructure standard
- **ISO/IEC 30134** — Data center resource efficiency
- **Schneider Electric DCCA** — Data center operations

---

## Tools & Technologies

![Grafana](https://img.shields.io/badge/Grafana-Capacity%20Dashboards-orange)
![Python](https://img.shields.io/badge/Python-Capacity%20Analysis-blue)
![DCIM](https://img.shields.io/badge/DCIM-Capacity%20Management-blue)
![Schneider](https://img.shields.io/badge/Schneider-EcoStruxure-green)
![Prometheus](https://img.shields.io/badge/Prometheus-Metrics-orange)
![Excel](https://img.shields.io/badge/Excel-Capacity%20Planning-green)

---

## Author

**George Amankwaa Sarpong**
Data Center Operations Engineer | Capacity Planning & Energy Management
📍 Accra, Ghana
🔗 [LinkedIn](https://linkedin.com/in/georgesarpong)
🌐 [GitHub Portfolio](https://github.com/GeorgeSarpong)

---

*This project is part of a broader portfolio demonstrating readiness for Data Center Operations Engineer and Critical Infrastructure Engineer roles in the US and global market.*
