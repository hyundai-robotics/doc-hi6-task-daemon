# 2.1. Settings

Select `[F2: system] - 4: Application parameters - 15: Task daemon`.

![Task Daemon Menu](../_assets/menu.png)

<br>

A settings screen like the one below will open.  
Settings can be configured for Task 1 to Task 7. (Task 0 cannot be used as a daemon.)

- If you enter a job number in the `job no.` field, the task will be set to run as a daemon using that number as the main program.  
If set to 0, that task will not be used as a daemon. In other words, it is in the daemon OFF state.

- Checking `Auto exe.` will automatically execute the daemon when the settings are completed or when the controller is booted.
- Checking `Repeat` will repeat the job CYCLE from the beginning once it is completed. This is conceptually the same as setting the `[F7: cond.set] - Operation cycle type` to `Continuous`.

- The `Status` field displays the current state of the task along with the current program counter (program number/step number/function number) in parentheses.

  - OFF: The state where it is not used as a daemon.
  - OCCUPIED: A task currently in use due to multitasking. It cannot be used as a daemon.
  - READY: The state waiting to be started from the program header.
  - RUN: The state where the daemon is currently playing.
  - STOP: The state where execution has stopped.
  - WAITING: The state waiting at `delay` statements, `wait` statements, `input` statements, etc.
  - ERROR: The state where an error has occurred. An error code may also be displayed.
  - END: The state where the job CYCLE has been completed.

![Task Daemon Settings Screen](../_assets/setting.png)

Manual operations can be performed on the currently selected task using the F keys at the bottom.

- `[F1: Reset]`: Stops the selected task and performs a reset. This is conceptually the same as executing `R0,[ENTER]`. All call information and local variables are cleared, and the program counter is reset to the main program header position.
- `[F2: Execution]`: Starts a task daemon in STOP, READY, or END state. This is conceptually the same as pressing the `START` button.
- `[F3: Stop]`: Stops a task daemon in RUN or WAITING state. This is conceptually the same as pressing the `STOP` button.

- `[F7: OK]`: Saves the settings and closes the settings screen. Task daemons set with `Auto exe.` will start execution.