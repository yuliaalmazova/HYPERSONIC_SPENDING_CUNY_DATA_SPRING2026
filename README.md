# Hypersonic Budget — FY 2027 RDT&E Analysis

Interactive dashboard for exploring the Department of Defense FY 2027 Presidential Budget Request, focused on hypersonic and missile defense programs (Exhibit R-1 RDT&E).

---

## Overview

This tool parses the full DoD RDT&E budget dataset, de-duplicates program elements by PE/BLI code, and organizes spending into two strategic domains:

- **Space-Focused** — SDA/PWSA Tracking Layers, Space Force programs, Golden Dome for America
- **Earth Battlefield** — Offensive strike missiles (CPS, HACM, LRHW), terrestrial MDA sensors, C2BMC infrastructure

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | React 18 |
| Build tool | Vite 6 |
| Styling | Tailwind CSS 4 |
| Data | DoD Exhibit R-1 CSV (public domain) |

---

## Getting Started

**Prerequisites:** Node.js 18+

```bash
# Install dependencies
npm install

# Start dev server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

The dev server runs at `http://localhost:5174` by default.

---

## Project Structure

```
hypersonic-budget/
├── public/
│   └── data.csv              # DoD RDT&E Exhibit R-1 source data
├── src/
│   ├── utils/
│   │   └── parse.js          # CSV parser, de-duplication, domain tagging, aggregation
│   ├── components/
│   │   ├── HeroStats.jsx     # Top-line FY25/26/27 summary cards
│   │   ├── TrendChart.jsx    # Year-over-year trend bar chart
│   │   ├── DomainBreakdown.jsx  # Space vs Earth donut/bar breakdown
│   │   ├── DomainPanel.jsx   # Sub-group detail panel per domain
│   │   └── ProgramTable.jsx  # Sortable full program table
│   ├── App.jsx               # Root layout, tab navigation, data loading
│   ├── main.jsx              # React entry point
│   └── index.css             # Tailwind base styles
├── index.html
├── vite.config.js
└── package.json
```

---

## Data Processing Logic

All logic lives in `src/utils/parse.js`.

### 1. Parse
Reads the raw CSV, skips the aggregate totals row and header row, and maps each line to a structured object with fiscal year fields (`fy25_total`, `fy26_total`, `fy27_total`, etc.).

### 2. De-duplicate by PE code
The same PE/BLI code can appear across multiple appropriation accounts (e.g. O&M, Procurement, RDT&E). Two strategies are applied:

- **Golden Dome (account `3007D`)** — funds are genuinely additive across budget activities, so all rows sharing a PE code are **summed**.
- **All other programs** — keep only the row with the **highest FY27 Total** per PE code to avoid double-counting pass-through funds.

### 3. Tag domains
Each program element is assigned to one of three domains based on its PE code:

| Domain | Programs included |
|---|---|
| `space` | SDA Tracking Layer (PWSA), Space Force, all Golden Dome PEs |
| `earth` | CPS, HACM, LRHW, Army/AF hypersonics, MDA sensors, C2BMC, BMD test infrastructure |
| `other` | Everything else in the RDT&E portfolio |

### 4. Aggregate & display
Helper functions `sumBy` and `groupBy` roll up totals by domain, sub-group, and service branch. Values are stored in `$thousands` and displayed as `$M` / `$B`.

---

## Dashboard Tabs

| Tab | Contents |
|---|---|
| Overview | Hero stats, trend chart, domain breakdown, collapsed domain panels |
| Space-Focused | Full sub-group detail for space domain programs |
| Earth Battlefield | Full sub-group detail for earth domain programs |
| All Programs | Sortable table of all de-duplicated PE codes |

---

## Data Source

**DoD FY 2027 Presidential Budget Request — Exhibit R-1 RDT&E Programs**
All values are in `$thousands`. Classification: UNCLASSIFIED // FOR OFFICIAL USE ONLY.

---

## Notes

- FY 2025 figures are actuals; FY 2026 reflects enacted + PL 119-21 Spend Plan; FY 2027 is the President's request.
- The Golden Dome for America fund (`3007D`) spans RDT&E, Procurement, and O&M appropriations — totals reflect the full proposed investment across all three.
- Programs tagged as `other` are visible in the All Programs tab but excluded from domain-specific totals.
