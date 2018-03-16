# track-time CLI

I recently discovered the Watson CLI tool for tracking time via the
command-line.

It's pretty smart, but after a few days of using it, I can see a similar system
with a different UI I would like to use.

Hence this document.

To start tracking a new task, just say you want to:

    track-time start <task-name>

To track a subtask within a task,

    track-time start <task-name> <subtask-name>

or if you're already working on a task,

    track-time subtask <subtask-name>

Starting a subtask saves a start time for it and any ancestor tasks that do not
already have one. If this task is already started, the current work block is
given a stop timestamp and a new start time is recorded.

Any non-ancestor tasks that have a start time but no stop time are marked as
stopped. This makes it easy to change the current task as a scripted action,
without worrying about what was previously active.

The 'start' and 'subtask' commands support a '--budget' option, which will
notify you when you reach the specified amount of time with the task active,
relative to the new start time.

    track-time start finances --budget '1 hour'

will notify you when cumulative time spent with the finances task active
reaches one hour relative to the newly-saved start time. That could be
implemented without a daemon process via the `at` command on *nix systems, with
a resolution of minutes, though it would involve some bookkeeping to cancel
scheduled reminders when the active task changes.

To add a note to a task:

    track-time note [task-name] [subtask-names...]

If the name is not specified it applies to the active task (it exits with a
nonzero status if no task is active in that case). It's handy if you want a log
for display on invoices or similar.

To stop tracking a task, just say

    track-time stop [task-name...]

If you do not pass a task name, it will stop the current task and leave all
ancestors active. If no task is running it will say so.

Subtasks can be nested to arbitrary depths, in theory. In practice an
implementation may limit depth.

Onr value of this approach is that you can use hooks to activate tasks based on
what file paths you are active in. For me, running a hook on focused buffer
change in Emacs and on prompt display in shell to check current dir would do
pretty well at setting my active task, especially if I added a 'current
subtask' concept to the project's metadata somehow (git branch notes?). I could
probably use that in a git commit-message hook to auto-insert issue references,
too, which I've wanted for years.

Timestamps should be in UTC with local timezone name and offset as metadata.

A sister command 'report-time' would be useful for showing how you actually
spent your time.

Possible extensions:

- persistent cyclical budgets
- custom command on budget exhaustion
