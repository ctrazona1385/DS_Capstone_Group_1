# DPC Clinic Siting Model
### A Data-Driven Framework for Identifying Optimal Direct Primary Care Locations Across U.S. Counties

---

## Overview

Access to primary care in the United States is increasingly stratified by geography and insurance status, leaving millions in **"care deserts"** — communities with few physicians, limited facilities, and high rates of uninsured residents.

**Direct Primary Care (DPC)** clinics offer a promising alternative: low-cost, subscription-based primary care that operates independently of insurance. However, identifying where such clinics can realistically be established — and where they can remain financially viable while serving underserved populations — requires systematic, data-driven analysis that currently does not exist at scale.

This project develops a **composite location scoring model** to identify optimal sites for DPC clinic placement across U.S. counties, balancing community need with operational viability.

---

## Objectives

The model integrates economic, demographic, and healthcare infrastructure indicators to support evidence-based **go/no-go siting decisions** that maximize care impact while ensuring financial sustainability.

**Three key deliverables:**

1. A reproducible data pipeline integrating health, geographic, and economic indicators at the county level
2. A composite scoring model with documented methodology and sensitivity analysis, enabling go/no-go recommendations for DPC clinic siting
3. An interactive choropleth map dashboard visualizing opportunity scores, underlying indicators, and estimated revenue potential by county

---

## Data Sources

| Source | Data Used |
|---|---|
| **Census Bureau** (ACS 5-year estimates & SAHIE) | Uninsured rates, income levels, demographic indicators |
| **HRSA** (HPSA & MUA designations) | Physician shortage areas, medically underserved area designations |
| **CMS** | Provider data, hospital infrastructure |
| **CDC PLACES** | Health outcomes at the county level |

**Key indicators examined:**
- Uninsured rates
- Physician shortages
- Hospital bed ratios
- Care facility proximity
- Income levels
- Health outcomes

---

## Methodology

### Composite Underserved Score

A weighted composite score is constructed from need indicators across all U.S. counties. Sensitivity analysis validates that findings are robust to methodological choices.

### Operational Viability & Revenue Model

Operational viability is assessed using a revenue model benchmarked against:

- **Direct Primary Care Coalition** — published DPC cost benchmarks providing direct operational reference
- **HRSA Uniform Data System (UDS)** — standardized annual cost and utilization reporting from all federally funded health centers, used as a *conservative upper bound* on expected costs
- **Peer-reviewed literature** on DPC economics

> Because DPC clinics typically operate leaner than Federally Qualified Health Centers, UDS data serves as a conservative upper bound on expected costs, with DPC Coalition benchmarks providing a more direct operational reference.

### Siting Tiers

Locations are classified into three actionable categories based on both need and estimated revenue viability:

| Tier | Label | Description |
|---|---|---|
| 🟢 | **Strong Go** | High need, financially viable |
| 🟡 | **Conditional** | Moderate need or viability; requires further assessment |
| 🔴 | **Not Viable** | Insufficient need or financial sustainability |

---

## Expected Outcomes

If successful, this project will deliver:

- A **reproducible data pipeline** integrating health, geographic, and economic indicators at the county level
- A **composite scoring model** with documented methodology and sensitivity analysis enabling go/no-go recommendations
- An **interactive choropleth map dashboard** visualizing opportunity scores, underlying indicators, and estimated revenue potential by county

---

## Impact & Applications

This project provides a replicable framework that **community health advocates, policymakers, and DPC operators** can apply to direct investment toward communities where subscription-based primary care can both serve the underserved and sustain itself financially.

---

## Repository Structure

```
├── data/
│   ├── raw/              # Source data from Census, HRSA, CMS, CDC
│   └── processed/        # Cleaned and merged county-level datasets
├── pipeline/             # Data ingestion and processing scripts
├── model/                # Composite scoring model and sensitivity analysis
├── dashboard/            # Interactive choropleth map application
├── docs/                 # Methodology documentation
└── README.md
```

---

## References

- U.S. Census Bureau — American Community Survey (ACS) 5-Year Estimates
- U.S. Census Bureau — Small Area Health Insurance Estimates (SAHIE)
- HRSA — Health Professional Shortage Area (HPSA) & Medically Underserved Area (MUA) Data
- CMS — Provider and Hospital Data
- CDC PLACES — County-Level Health Outcomes
- Direct Primary Care Coalition — DPC Economic Benchmarks
- HRSA Uniform Data System (UDS) — Federally Qualified Health Center Reporting
