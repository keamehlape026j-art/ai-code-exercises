# Algorithm Deconstruction Journal

## Algorithm 1: Task Priority Sorting Algorithm

### Overview

The Task Priority Sorting Algorithm calculates an importance score for each task and then sorts the tasks from the highest score to the lowest score.

The score is based on several factors:

- Task priority
- Due date
- Task status
- Special tags
- How recently the task was updated

The algorithm can also return only the top five highest-scoring tasks by default.

---

## 1. Understanding `calculate_task_score()`

The `calculate_task_score(task)` function calculates a numerical score for one task.

### Step 1: Base Priority Score

Each priority has a weight:

- LOW = 1
- MEDIUM = 2
- HIGH = 4
- URGENT = 6

The weight is multiplied by 10.

For example:

- LOW = 10 points
- MEDIUM = 20 points
- HIGH = 40 points
- URGENT = 60 points

This means priority has a strong influence on the final score.

### Step 2: Due Date

If the task has a due date, the algorithm checks how close the due date is.

- Overdue = +35 points
- Due today = +20 points
- Due within 2 days = +15 points
- Due within 7 days = +10 points

Tasks that are overdue or due soon therefore receive additional points.

### Step 3: Task Status

The algorithm reduces the score for tasks that are already completed or under review.

- DONE = -50 points
- REVIEW = -15 points

This makes completed tasks much less likely to appear near the top of the priority list.

### Step 4: Important Tags

The algorithm checks whether the task contains one of these tags:

- `blocker`
- `critical`
- `urgent`

If it finds one, the task receives an additional +8 points.

### Step 5: Recently Updated Tasks

The algorithm checks when the task was last updated.

If it was updated less than one day ago, it receives +5 points.

### Final Score

All these values are combined to produce the task's final importance score.

```text
Priority points
      +
Due date points
      +
Status adjustment
      +
Important tag bonus
      +
Recent update bonus
      =
Final task score


2. Understanding sort_tasks_by_importance()
The sort_tasks_by_importance(tasks) function sorts a collection of tasks.
First, it calculates a score for every task.
It stores each score together with its task:
(score, task)
The tasks are then sorted using the calculated score.
The reverse=True argument means the highest score appears first.
Therefore:
Highest score
      ↓
    Task A
      ↓
    Task B
      ↓
    Task C
      ↓
Lowest score

3. . Understanding get_top_priority_tasks()
The get_top_priority_tasks(tasks, limit=5) function returns only the highest-priority tasks.
It first calls:
sort_tasks_by_importance(tasks)
Then it takes the first limit tasks.
By default, the limit is 5.

For example:
100 → Task A
85  → Task B
70  → Task C
60  → Task D
50  → Task E
40  → Task F

With a limit of 5, the result would be
Task A
Task B
Task C
Task D
Task E

4. Complete Algorithm Flow
List of tasks
     ↓
Calculate score for each task
     ↓
Check priority
     ↓
Check due date
     ↓
Check status
     ↓
Check important tags
     ↓
Check recent update
     ↓
Final score for each task
     ↓
Sort scores from highest to lowest
     ↓
Return sorted tasks
     ↓
Optional: Return only top N tasks

5.Example
Suppose a task has:
HIGH priority
Due tomorrow
Status TODO
Tag critical
Updated today

Its score would be:
HIGH priority:       40
Due within 2 days:  +15
TODO:                 0
Critical tag:         +8
Updated today:        +5
------------------------
Total:                68

The final score would therefore be 68.

Key Insight
The algorithm does not simply sort tasks based on their priority value.
Instead, it combines several factors to calculate an overall importance score.
This means a lower-priority task could potentially rank highly if it is overdue, has an important tag, or was recently updated.


7. Learning Points
I learned that the algorithm uses a weighted scoring system instead of a simple priority sort.
I also learned that different factors can increase or decrease a task's score.
The algorithm separates the scoring process from the sorting process:
calculate_task_score() calculates importance.
sort_tasks_by_importance() orders tasks.
get_top_priority_tasks() limits the results.
This makes each function responsible for a specific part of the process.

