# Welcome back, Carlos

---

## Quick Access

| | |
|---|---|
| [[Projects/Skymek/Deployment/Deployment Guide\|Skymek Deploy]] | [[Projects/Negocia VIP/Deployment/Deployment Guide\|Negocia Deploy]] |
| [[Projects/Kanban\|Kanban Board]] | [[Infrastructure/Keycloak Configuration\|Keycloak]] |
| [[Tracker\|Activity Tracker]] | [[Daily Notes/2026-03-26\|Today]] |

---

## Projects

```dataview
TABLE WITHOUT ID
  file.folder AS "Project",
  length(file.outlinks) AS "Links",
  file.mtime AS "Last Modified"
FROM "Projects"
WHERE file.name = "README" OR file.name = "Deployment Guide" OR file.name = "Documentation"
SORT file.mtime DESC
```

---

## Recent Activity

```dataview
TABLE WITHOUT ID
  file.name AS "Note",
  file.folder AS "Location",
  file.mtime AS "Modified"
FROM ""
WHERE file.name != "Home" AND !contains(file.path, "Templates")
SORT file.mtime DESC
LIMIT 8
```

---

## Tasks

```dataview
TASK
FROM "Projects" OR "Daily Notes"
WHERE !completed
LIMIT 15
```

---

## Deployment Guides

```dataview
LIST
FROM "Projects"
WHERE contains(file.name, "Deployment") OR contains(file.name, "Deploy")
```

---

## Vault Stats

- Total notes: `$= dv.pages().length`
- Projects: `$= dv.pages('"Projects"').length`
- Infrastructure: `$= dv.pages('"Infrastructure"').length`
- Learning: `$= dv.pages('"Learning"').length`
