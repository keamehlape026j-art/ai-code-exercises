Code Understanding Journal

Part 1: Understanding a Specific Feature

Feature explored

Task creation and task status updates.

Main components involved

The main components involved are "cli.py", "TaskManager", "Task", "TaskStorage", "TaskEncoder", "TaskDecoder", "TaskStatus", and "TaskPriority".

"cli.py" handles commands entered by the user. "TaskManager" coordinates task operations. "Task" represents an individual task and contains its state and behaviour. "TaskStorage" handles storing and retrieving tasks. "TaskEncoder" and "TaskDecoder" handle conversion between Python task objects and JSON data.

Task creation flow

When a user creates a task, the command is received by "cli.py". The CLI calls "TaskManager.create_task()". The TaskManager converts the priority and due date and creates a new "Task" object.

The "Task" object receives a unique ID, starts with a "TODO" status, and records its creation and update times. The task is then passed to "TaskStorage.add_task()". The task is added to the in-memory dictionary and saved to "tasks.json".

The flow is:

User → "cli.py" → "TaskManager.create_task()" → "Task" → "TaskStorage.add_task()" → "save()" → "tasks.json".

Status update flow

When a task status is changed, "cli.py" calls "TaskManager.update_task_status()".

For statuses other than "DONE", the TaskManager calls "TaskStorage.update_task()", which updates the task and saves the changes.

When the new status is "DONE", the TaskManager retrieves the task using "get_task()", calls "task.mark_as_done()", and then saves the changes.

The completion flow is:

User → "cli.py" → "TaskManager.update_task_status()" → "TaskStorage.get_task()" → "Task.mark_as_done()" → "TaskStorage.save()" → "tasks.json".

What happens when a task is completed?

The "mark_as_done()" method changes the task status to "DONE". It also records the current time in "completed_at" and updates "updated_at" to the same time.

This means the application records both the completion state and the time when the task was completed.

How data is stored and retrieved

Tasks are stored in memory using the "self.tasks" dictionary in "TaskStorage".

The tasks are permanently stored in "tasks.json". "TaskEncoder" converts Python task objects into JSON-compatible data, including converting datetime values into ISO-format strings.

When the application starts, "TaskStorage.load()" reads the JSON file. "TaskDecoder" converts the stored data back into "Task" objects and places them into the in-memory dictionary.

Interesting design approach

An interesting design approach is the separation of responsibilities. The CLI handles user commands, TaskManager coordinates operations, Task handles task state and behaviour, and TaskStorage handles persistence.

This separation makes it easier to understand which part of the application is responsible for each operation.

Key understanding

The main thing I learned is that creating or updating a task involves several components rather than one function. The CLI starts the process, TaskManager coordinates it, the Task object represents the data and behaviour, and TaskStorage makes sure the changes are persisted in "tasks.json".

## Part 2: Task Prioritization

### Initial Understanding

I initially understood task prioritization as a system where tasks are given different priority levels and the most important tasks would automatically be placed before less important tasks.

### What I Discovered

After examining `models.py`, `app.py`, and `storage.py`, I discovered that the task manager has four priority levels: LOW, MEDIUM, HIGH, and URGENT. A new task has MEDIUM priority by default.

The priority can be changed when creating a task or later using `update_task_priority()`. Tasks can also be filtered by a specific priority using `get_tasks_by_priority()`. The system also counts tasks according to their priority in the statistics function.

However, the system does not automatically sort or reorder all tasks according to priority. The priority is stored as an enum value and is used for filtering and statistics.

### Key Insights from the Guided Questions

The guided questions helped me understand the difference between assigning a priority and actually sorting tasks by priority. I learned that the priority value is stored with the task and can be changed, filtered, and counted, but there is no code that automatically puts URGENT tasks before HIGH, MEDIUM, or LOW tasks.

I also learned that the priority values are stored as numbers: LOW is 1, MEDIUM is 2, HIGH is 3, and URGENT is 4.

### Misconceptions Clarified

My main misconception was that assigning a higher priority would automatically cause the task to appear before lower-priority tasks. After examining the code, I discovered that this application does not implement automatic priority sorting. Priority is mainly used to describe, filter, update, store, and count tasks.



Part 3: Mapping Data Flow and State Management

Task Completion Data Flow

The data flow when a task is marked as complete is:

User marks task as DONE
        ↓
TaskManager.update_task_status()
        ↓
TaskStorage.get_task(task_id)
        ↓
Task.mark_as_done()
        ↓
Status changes to DONE
        ↓
completed_at is set to current time
        ↓
updated_at is set to completion time
        ↓
TaskStorage.save()
        ↓
TaskEncoder converts the task to JSON
        ↓
tasks.json is updated

State Changes

When a task is marked as complete, the following state changes occur:

1. The task's status changes from its previous status to "TaskStatus.DONE".
2. "completed_at" is set to the current date and time.
3. "updated_at" is set to the same completion time.
4. The updated task is saved to the JSON storage file.

Potential Points of Failure

There are several possible points where the process could fail:

- An invalid task ID could mean that the task cannot be found.
- An invalid status value could cause the "TaskStatus" conversion to fail.
- The task storage file may not be available or writable.
- Saving the task to "tasks.json" could fail because of a file or storage error.
- The JSON encoding process could fail if unexpected data is present.

How the Application Persists Changes

The application retrieves the task from "TaskStorage" and calls "mark_as_done()" to change its state. It then calls "storage.save()".

The "save()" method writes all tasks to "tasks.json" using "TaskEncoder". The encoder converts the task's status to its string value and converts datetime values such as "completed_at" and "updated_at" into ISO-format strings. This allows the completed state to be stored and loaded again later.

Key Insight

The task completion process is handled across several components rather than in one place. "TaskManager" coordinates the operation, "Task" performs the state changes, and "TaskStorage" handles persistence. This separation makes it easier to understand which component is responsible for each part of the process.


Part 4 Codebase Exploration Challenge — Presentation

Good morning everyone.

For this exercise, I explored a Python task management application to understand how the code works without making changes to it.

1. High-Level Application Architecture

The application is divided into different components, with each component having a specific responsibility.

The "models.py" file contains the main task model and defines task priorities and statuses.

The "app.py" file contains the "TaskManager", which coordinates actions such as creating tasks, updating tasks, changing priorities, and completing tasks.

The "storage.py" file handles saving and loading tasks using a JSON file called "tasks.json".

This separation helped me understand how the different parts of the application work together.

2. Task Creation

When a task is created, the "TaskManager" receives information such as the title, description, priority, due date, and tags.

A "Task" object is then created in "models.py". Each task receives a unique ID, a default status of TODO, a creation time, and a priority.

The task is then passed to "TaskStorage", which stores it and saves the information to "tasks.json".

3. Task Prioritization

The application has four priority levels: LOW, MEDIUM, HIGH, and URGENT.

A new task has MEDIUM priority by default, but the priority can be changed later.

One important thing I discovered was that the application does not automatically sort tasks from URGENT to LOW. Instead, it can filter tasks by a selected priority and count tasks according to their priority.

This corrected my initial assumption about how prioritization worked.

4. Task Completion

When a task is marked as complete, "TaskManager.update_task_status()" finds the task and calls "mark_as_done()".

The task's status changes to DONE. The application also records the completion time in "completed_at" and updates "updated_at".

The changes are then saved to "tasks.json".

5. Interesting Design Approach

One interesting approach I discovered was the separation of responsibilities between the classes.

"TaskManager" coordinates operations, "Task" manages the task's state, and "TaskStorage" handles persistence.

The application also uses an encoder and decoder to convert task objects and dates to and from JSON. This makes it possible to save the tasks and load them again later.

6. What I Found Challenging

The most challenging part was understanding how the different files worked together.

At first, I thought that task priority meant that urgent tasks would automatically appear before lower-priority tasks. By examining the code and following the guided questions, I discovered that priority is used mainly for assigning, updating, filtering, and counting tasks.

The prompts helped me because they gave me questions to investigate instead of simply giving me an answer. This encouraged me to look at the code myself and compare my initial understanding with what the code actually does.

Conclusion

Overall, this exercise taught me that understanding an existing codebase is not only about reading individual lines of code. It is also about following the flow of data between different components and checking whether my assumptions are supported by the code.

Thank you.

Part 3: Mapping Data Flow and State Management

Task Completion Data Flow

The data flow when a task is marked as complete is:

User marks task as DONE
        ↓
TaskManager.update_task_status()
        ↓
TaskStorage.get_task(task_id)
        ↓
Task.mark_as_done()
        ↓
Status changes to DONE
        ↓
completed_at is set to current time
        ↓
updated_at is set to completion time
        ↓
TaskStorage.save()
        ↓
TaskEncoder converts the task to JSON
        ↓
tasks.json is updated

State Changes

When a task is marked as complete, the following state changes occur:

1. The task's status changes from its previous status to "TaskStatus.DONE".
2. "completed_at" is set to the current date and time.
3. "updated_at" is set to the same completion time.
4. The updated task is saved to the JSON storage file.

Potential Points of Failure

There are several possible points where the process could fail:

- An invalid task ID could mean that the task cannot be found.
- An invalid status value could cause the "TaskStatus" conversion to fail.
- The task storage file may not be available or writable.
- Saving the task to "tasks.json" could fail because of a file or storage error.
- The JSON encoding process could fail if unexpected data is present.

How the Application Persists Changes

The application retrieves the task from "TaskStorage" and calls "mark_as_done()" to change its state. It then calls "storage.save()".

The "save()" method writes all tasks to "tasks.json" using "TaskEncoder". The encoder converts the task's status to its string value and converts datetime values such as "completed_at" and "updated_at" into ISO-format strings. This allows the completed state to be stored and loaded again later.

Key Insight

The task completion process is handled across several components rather than in one place. "TaskManager" coordinates the operation, "Task" performs the state changes, and "TaskStorage" handles persistence. This separation makes it easier to understand which component is responsible for each part of the process