# Data files used by dashboard-all.html

## CRRID (project-scoped exports, pre-existing)
- `SubArea.csv` — CRRID sub-areas (subAreaId, subAreaName, areaId, ...)
- `Task.csv` — CRRID tasks, with real dates (createdAt, completedAt, endDate, ...)
- `User.csv` — CRRID task managers/users

## Homeland Global Park (live-pulled snapshot, added 2026-09-01)
Global Park's original exports (`AllAreas.csv`, `AllProjects.csv`) only had
area/project-level rollup counts — no per-task rows, dates, sub-areas, or
managers. To give Global Park the same task-list drill-down and accurate
monthly chart that CRRID has, its task-level data was pulled directly from
the live Homeland Group FMS database (`https://asap.homelandgroup.org/api/db`)
using a bearer token the user supplied, and saved as static CSV snapshots:

- `GlobalParkSubArea.csv` — 177 rows (subAreaId, subAreaName, areaId)
- `GlobalParkTask.csv` — 797 rows (taskId, taskName, subAreaId, taskManager, completed, createdAt, completedAt, startDate, endDate)
- `GlobalParkUser.csv` — 1 row (userId, firstName, lastName, username, email)

Row counts were verified against `AllProjects.csv`'s Global Park totals
(`tasks=797`, `subAreas=177`) — they match exactly.

**This is a one-time snapshot, not a live connection.** The bearer token
was only used transiently in a browser session to fetch this data — it was
never written to any file in this folder (this folder is linked to a
public GitHub repo, so committing a live credential here would expose it).
The dashboard reads these CSVs as static files, the same way it reads the
CRRID CSVs; it does not call the live API and will not pick up new tasks
automatically.

**To refresh this data later:** re-run the same live pull (fetch
`SubArea`, `Task`, `User` from the API filtered to Global Park's
area/tower IDs, using a fresh bearer token) and overwrite these three
CSV files. If you want automatic refreshing instead of a manual
snapshot, that requires either (a) a small backend/proxy that holds the
token server-side and the dashboard calls, since a public static site
can't safely hold a live credential, or (b) a scheduled job that re-pulls
and re-commits these CSVs periodically.
