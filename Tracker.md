---
tags:
  - tracker
---

# Activity Tracker

---

## Commits per Day

```tracker
searchType: frontmatter
searchTarget: commits
folder: Daily Notes
datasetName: Commits
line:
  title: "Commits"
  yAxisLabel: "Commits"
  lineColor: "#4ea8de"
  fillGap: true
```

---

## Hours Worked

```tracker
searchType: frontmatter
searchTarget: hoursWorked
folder: Daily Notes
datasetName: Hours
line:
  title: "Hours Worked"
  yAxisLabel: "Hours"
  lineColor: "#a6e3a1"
  fillGap: true
```

---

## Commits Summary

```tracker
searchType: frontmatter
searchTarget: commits
folder: Daily Notes
summary:
  template: "Total commits: {{sum}}\nAverage per day: {{average}}\nMax in a day: {{max}}\nDays tracked: {{count}}"
```

---

## Hours Summary

```tracker
searchType: frontmatter
searchTarget: hoursWorked
folder: Daily Notes
summary:
  template: "Total hours: {{sum}}\nAverage per day: {{average}}\nMax in a day: {{max}}\nDays tracked: {{count}}"
```

---

## Tasks Completed

```tracker
searchType: task.done
searchTarget: ""
folder: Daily Notes
line:
  title: "Tasks Completed"
  yAxisLabel: "Tasks"
  lineColor: "#cba6f7"
  fillGap: true
```

---

## Habit Tracker

```tracker
searchType: task.done
searchTarget: Code review, Deploy check, Documentation
folder: Daily Notes
datasetName: Code Review, Deploy Check, Docs
month:
  title: "Habits - This Month"
  color: "#4ea8de"
```

---

## Notes Created per Day

```tracker
searchType: fileMeta
searchTarget: numWords
folder: ""
summary:
  template: "Total words in vault: {{sum}}\nAverage words per note: {{average}}"
```
