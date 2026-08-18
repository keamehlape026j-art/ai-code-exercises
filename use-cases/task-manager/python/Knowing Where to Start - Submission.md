# Knowing Where to Start - Submission

## Initial vs. Final Understanding

At the beginning of the exercise, I had only a basic understanding of the Task Manager based on the file names. I initially thought that `app.py` was the main entry point and that `models.py` contained the task information.

After exploring the code, I developed a much clearer understanding of how the components work together.

The main flow is:

User → `cli.py` → `TaskManager` in `app.py` → `models.py` / `storage.py` → `tasks.json`

I learned that `cli.py` is responsible for the command-line interface, `app.py` coordinates task operations, `models.py` contains the domain model, and `storage.py` handles persistence.

The application is written in Python and mainly uses Python's standard library.

## Valuable Insights From Each Prompt

### Project Structure Prompt

This prompt helped me understand the responsibilities of the different files and identify the application's overall architecture.

I learned that understanding the structure first makes it easier to know where to look when investigating a feature.

### Feature Location Prompt

This prompt helped me think about where a new feature should fit into the existing codebase.

For the hypothetical CSV export feature, I identified `cli.py`, `app.py`, and `storage.py` as the main components that would potentially be involved.

### Domain Understanding Prompt

This prompt helped me understand the relationship between `Task`, `TaskStatus`, and `TaskPriority`.

I learned that priority and status are separate concepts. A task's priority does not automatically change its status.

## Approach to the New Business Rule

The business rule was:

"Tasks that are overdue for more than 7 days should be automatically marked as abandoned unless they are marked as high priority."

I would first add an `ABANDONED` status to the `TaskStatus` enum.

I would then add logic to identify tasks that have been overdue for more than seven days. Tasks that meet the condition would be marked as abandoned unless they are protected by the required priority rule.

The main files I would investigate are:

- `models.py` — for the new status and task-related business logic.
- `app.py` — for coordinating the business rule.
- `storage.py` — for retrieving and saving task changes.
- `cli.py` — if the new status needs to be displayed or managed through the command line.

Before implementing the rule, I would clarify requirements with the team, especially whether `URGENT` tasks should also be protected and how the automatic check should be triggered.

## Strategies I Developed

I learned several strategies for approaching an unfamiliar codebase:

1. Start with the directory structure and file names.
2. Identify the application's entry point.
3. Understand the responsibility of each major component.
4. Follow the flow of data between components.
5. Search for existing implementations similar to the feature I need.
6. Understand the domain model before changing business logic.
7. Write down assumptions and verify them against the code.
8. Ask questions about unclear business requirements before implementing.
9. Use AI prompts to guide exploration rather than relying only on AI-generated answers.
10. Check tests, documentation, configuration files, and Git history when available.

## Final Reflection

The biggest lesson from this exercise is that I do not need to understand every line of code immediately when joining an unfamiliar project.

I can start with the structure, identify the important components, follow the data flow, and gradually build a deeper understanding.

The AI prompts helped me ask better questions and approach the codebase systematically. I feel more confident about finding where a feature belongs and understanding the existing design before making changes.