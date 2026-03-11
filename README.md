# MNP Fraud Intelligence Dashboard
### Lyca Mobile Italy — Mobile Number Portability Retailer Analysis

A single-file, zero-dependency HTML dashboard for detecting fake port-in activations and KB (kickback) incentive fraud across the Lyca Mobile Italy retail network.

---

## Overview

Italian MVNO retailers earn a kickback bonus when a ported-in customer remains active beyond **60 days**. This creates a fraud incentive: retailers coach customers to make one top-up around day 50–55 to trigger the KB threshold, after which the customer churns. The standard 60-day retention metric misses this entirely.

This tool detects fraud using two independent signals:

| Signal | What it catches |
|---|---|
| **90d+ True Retention** (`active_grt_90 / total_ports`) | Customers genuinely active beyond 90 days — cannot be gamed cheaply |
| **KB > Total Revenue** (`total_KB > first_bundlevalue + Renewal_Amt`) | Retailers where Lyca paid more in kickbacks than it earned in subscription revenue — a direct net loss |

---

## Features

- **Overview page** — network KPIs, monthly retention gap chart with data-maturity shading, KB vs revenue trends, risk distribution
- **All Retailers page** — sortable/filterable table of all 88 retailers with colour-coded metrics; click any row for a modal with monthly breakdown, retention funnel and KB vs revenue charts
- **Fraud Detection page** — tabbed view of High Risk (21), Medium Risk (14) and KB > Revenue (18) retailers, with per-retailer analysis and recommended action; scatter plots and a retention waterfall for all retailers

### Data Maturity Awareness

Activations from **Nov 2025 onward** do not yet have complete 90-day data (data cut-off: mid-Feb 2026). The dashboard flags these months explicitly:

| Status | Months | Notes |
|---|---|---|
| ✅ Mature | Jan – Oct 2025 | Full 90d+ data — used for fraud scoring |
| ⚠ Partial | Nov 2025 | ~105 days elapsed — 90d borderline, 90d+ incomplete |
| ○ Immature | Dec 2025 – Feb 2026 | No 90d data — excluded from fraud scoring |

---

## Risk Scoring Model

Scores are calculated using **mature-month data only** (Jan–Oct 2025) where a retailer has ≥ 15 mature-month port-ins.

| Signal | Points |
|---|---|
| 90d+ retention < 10% | +5 |
| 90d+ retention < 15% | +4 |
| 90d+ retention < 30% | +2 |
| 60d stay ≥ 25% AND 90d stay < 15% (threshold gaming) | +4 |
| 60→90d churn > 70% (≥ 5 actives at 60d) | +3 |
| 60→90d churn > 50% (≥ 5 actives at 60d) | +2 |
| KB exceeds total revenue (KB > €500) | +3 |
| KB / Revenue ≥ 80% | +2 |
| KB / Revenue ≥ 60% | +1 |
| Port-out ≤ 60d > 50% | +2 |
| Port-out ≤ 60d > 25% | +1 |
| Renewal rate < 20% | +2 |
| Renewal rate < 50% | +1 |
| Clawback > €200 | +2 |
| Clawback > €50 | +1 |

**≥ 7 = HIGH RISK · 4–6 = MEDIUM RISK · < 4 = LOW RISK**

---

## Data

| Field | Description |
|---|---|
| `AccMgrid` | Account manager ID |
| `HotspotId` | Regional hotspot grouping |
| `retailerid` | Unique retailer identifier |
| `Month` | Activation month (YYYY-MM) |
| `Total_cnt` | Total port-ins in month |
| `active_30/60/90` | Customers still active at 30/60/90 days |
| `active_grt_90` | Customers active beyond 90 days (true retention) |
| `portout_60/120/grt_120` | Customers who ported out within 60/120/120+ days |
| `first_bundlevalue` | Total value of first bundles purchased |
| `Renewal_cnt` / `Renewal_Amt` | Renewal count and revenue |
| `Total_KB` | Total kickback paid to retailer |
| `clawback` | KB recovered (negative = money returned to Lyca) |

Source: `MNP_ANALYSE.xlsx` · Jan 2025 – Feb 2026 · 88 retailers · 986 rows

---

## Usage

No build step, no dependencies, no server required.

```bash
# Clone the repo
git clone https://github.com/hotspot-gif/MNP.git

# Open in any browser
open INDEX.html
```

All data is embedded in the HTML file. Works fully offline.

---

## Tech Stack

- Vanilla HTML / CSS / JavaScript
- [Chart.js 4.4.1](https://www.chartjs.org/) via CDN (cdnjs)
- [Google Fonts](https://fonts.google.com/) — Syne, DM Sans, IBM Plex Mono

---

## Repository Structure

```
MNP/
├── INDEX.html      # Complete self-contained dashboard (all data embedded)
├── README.md       # This file
└── LICENSE         # MIT License
```

---

## License

MIT — see [LICENSE](./LICENSE)
