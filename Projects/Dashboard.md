---
tags:
  - dashboard
---

# Projects Dashboard

## Active Projects

```dataview
TABLE status, created, tags
FROM "Projects"
WHERE status = "active"
SORT created DESC
```

## All Projects

```dataview
TABLE file.folder AS "Location", length(file.inlinks) AS "Links"
FROM "Projects"
WHERE file.name != "Dashboard"
SORT file.folder ASC
```

## Recent Changes

```dataview
TABLE file.mtime AS "Modified", file.folder AS "Folder"
FROM "Projects"
SORT file.mtime DESC
LIMIT 10
```

## Deployment Guides

```dataview
LIST
FROM "Projects"
WHERE contains(file.name, "Deployment") OR contains(file.name, "Deploy")
```
