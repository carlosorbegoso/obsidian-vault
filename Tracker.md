# Activity Tracker

---

## Stats

```start-multi-column
ID: stats
Number of Columns: 2
Largest Column: standard
```

**Commits**

```tracker
searchType: frontmatter
searchTarget: commits
folder: Daily Notes
summary:
    template: "Total: {{sum()}}\nAvg/day: {{average()}}\nBest day: {{max()}}\nDays: {{numTargets()}}"
```

--- column-end ---

**Hours**

```tracker
searchType: frontmatter
searchTarget: hoursWorked
folder: Daily Notes
summary:
    template: "Total: {{sum()}}\nAvg/day: {{average()}}\nMax: {{max()}}\nDays: {{numTargets()}}"
```

=== end-multi-column

---

## Commits
```tracker
searchType: frontmatter
searchTarget: commits
folder: Daily Notes
line:
    yAxisLabel: Commits
    lineColor: "#4ea8de"
    fillGap: true
    showPoint: true
    pointSize: 5
```

## Hours Worked
```tracker
searchType: frontmatter
searchTarget: hoursWorked
folder: Daily Notes
line:
    yAxisLabel: Hours
    lineColor: "#a6e3a1"
    fillGap: true
    showPoint: true
    pointSize: 5
```

## Commits (Bars)
```tracker
searchType: frontmatter
searchTarget: commits
folder: Daily Notes
bar:
    yAxisLabel: Commits
    barColor: "#4ea8de"
```

## Mood
```tracker
searchType: frontmatter
searchTarget: mood
folder: Daily Notes
line:
    yAxisLabel: Mood (1-5)
    lineColor: "#fab387"
    fillGap: true
    showPoint: true
    pointSize: 6
    yMin: 1
    yMax: 5
```

---

## Habit Calendars

### Code Review
```tracker
searchType: task.done
searchTarget: Code review
folder: Daily Notes
month:
    color: "#4ea8de"
    headerMonthColor: "#74c7ec"
    dimNotInMonth: true
    startWeekOn: Mon
```

### Deploy Check
```tracker
searchType: task.done
searchTarget: Deploy check
folder: Daily Notes
month:
    color: "#a6e3a1"
    headerMonthColor: "#74c7ec"
    dimNotInMonth: true
    startWeekOn: Mon
```

### Exercise
```tracker
searchType: task.done
searchTarget: Exercise
folder: Daily Notes
month:
    color: "#cba6f7"
    headerMonthColor: "#74c7ec"
    dimNotInMonth: true
    startWeekOn: Mon
```

---

## Words Written
```tracker
searchType: fileMeta
searchTarget: numWords
folder: Daily Notes
summary:
    template: "Total words: {{sum()}} | Average per note: {{average()}}"
```
