# Charting the Course for Maji Ndogo's Water Future (Part 4)

This project is the final implementation phase of the Maji Ndogo water analysis series. It converts analytical findings into an actionable engineering job list that can be executed and tracked in the `md_water_services` database.

## Project Objectives

- Build a unified analytical view across core operational tables.
- Quantify water access patterns at province and town levels.
- Prioritize interventions that benefit the largest populations.
- Translate business rules into SQL-driven improvement actions.
- Generate and maintain a `Project_progress` table for engineering execution tracking.

## Main Analysis Notebook

- Notebook: [project4.ipynb](project4.ipynb)
- Database connection uses `ipython-sql` + `PyMySQL`.
- SQL is executed through `%%sql` cells.

## Data Sources Used

The notebook integrates these core tables from `md_water_services`:

- `location`
- `visits`
- `water_source`
- `well_pollution`

## Core SQL Artifacts Produced

- View: `combined_analysis_table`
  - Joins location, visit, source, and pollution context for first visits (`visit_count = 1`).
- Temporary table: `town_aggregated_water_access`
  - Aggregates source-access percentages per town.
- View: `Project_progress_query`
  - Filters only sources requiring intervention.
- Table: `Project_progress`
  - Final implementation backlog and execution-tracking table for engineers.

## Improvement Rules Implemented

The notebook applies rule-based actions using SQL `CASE` logic:

1. `river` → `Drill well`
2. `well` + chemical contamination → `Install RO filter`
3. `well` + biological contamination → `Install UV and RO filter`
4. `shared_tap` with queue time `>= 30` minutes → `Install X taps nearby` where `X = FLOOR(time_in_queue / 30)`
5. `tap_in_home_broken` → `Diagnose local infrastructure`

## Analysis & Planning Workflow

1. Validate source tables and join paths.
2. Build `combined_analysis_table` for unified analysis.
3. Compute province-level water source percentages.
4. Compute town-level water source percentages and broken-tap ratios.
5. Summarize priorities and define practical intervention strategy.
6. Create `Project_progress` schema for delivery tracking.
7. Populate `Project_progress` from `Project_progress_query` using deterministic improvement rules.

## Key Findings Captured in Notebook

- High dependence on shared taps and rural water points drives queue pressure.
- A significant share of in-home tap infrastructure is non-functional in priority towns.
- A meaningful portion of well sources require contamination-specific treatment.
- Province- and town-level outputs are used to rank intervention urgency.

## Environment Setup

1. Create and activate a virtual environment.
2. Install dependencies:

```bash
pip install -r requirements.txt
```

3. Ensure a local MySQL database is available:
   - Host: `127.0.0.1`
   - Port: `3306`
   - Database: `md_water_services`

4. Update credentials in the notebook connection cell if needed:

```python
%sql mysql+pymysql://<username>:<password>@127.0.0.1:3306/md_water_services
```

## How to Run

1. Open `project4.ipynb`.
2. Run cells from top to bottom to ensure all views/tables are created in sequence.
3. Re-run the final insertion section as needed to refresh `Project_progress`.

> Note: The notebook uses a safe-update-compatible delete before re-insert:
>
> `DELETE FROM Project_progress WHERE project_id IS NOT NULL;`

## Repository Structure

```text
.
├── project4.ipynb
├── requirements.txt
└── README.md
```

## Notes

- CTEs in MySQL are scoped to a single query; reusable logic is persisted via views/temp tables.
- The project is designed to move from insight generation to operational execution in the same notebook.
- `Project_progress` includes status and completion metadata to support accountability over time.
