
[__SOURCE](README.md)
# ${cont_model} Robot Controller Function Manual - Task Daemon
[__SOURCE](1-overview/README.md)
# 1. Overview
[__SOURCE](1-overview/1-prerequisite.md)
# 1.1 Prerequisites

To understand this manual well, you should have the following knowledge.

* [${cont_model} Robot Controller Operation Manual](https://hrbook-hrc.web.app/#/view/doc-hi6-operation/en-tp630/README)
[__SOURCE](1-overview/2-about.md)
# 1.2 About the Task Daemon Function

{% hint style="info" %}
This feature is supported from V60.30-00 and later versions.
{% endhint %}

Generally, the job program of the ${cont_model} controller is executed only in automatic mode or when the StepFWD is pressed in manual mode.

However, there might be instances where it is necessary to run the job program in the background even when these playback conditions are not met. 
For example, if a network service function that reports the current status of the controller externally is implemented as a job, it would be beneficial for this job to always run regardless of the aforementioned playback conditions.

By utilizing the Task Daemon function, you can assign a specific job to a desired task and ensure it runs continuously regardless of playback conditions.

{% hint style="info" %}
A daemon refers to a program that runs continuously in the background, usually handling service requests from external systems.
{% endhint %}

{% hint style="warning" %}

The TaskDaemon function has the following restrictions:

- Step commands such as `move` are ignored without errors. In other words, the robot or auxiliary axes cannot be moved.

- Task 0 or task numbers in use by multitasking features cannot be used as daemons.

- Most commands such as the ones listed below do not function.

```python
cowork, axisctrl, filter, brake_check, gasp_check, softxyz, fctrl, softjoint, toolchng, load_esti, etc...
```

- Most robotic application commands do not function.

```python
arcon, lvs, multipass, cv.wait, heightsen, etc...
```

- Editing jobs running as TaskDaemon may, in some cases, stop the daemon execution of that task.

{% endhint %}
[__SOURCE](2-how-to-use/README.md)
# 2. How to Use
[__SOURCE](2-how-to-use/1-setting.md)
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
[__SOURCE](2-how-to-use/2-monitoring.md)
# 2.2. Monitoring

![Task daemon monitoring](../_assets/monitoring.png)

In the `pane layout - [F1: select] - multitask` monitoring window, you can check the current program counter of the task daemon.
[__SOURCE](2-how-to-use/3-job-editing.md)
# 2.3. Editing jobs

The main job program or sub job programs of the task daemon can be edited freely. However, if you perform command insertion/deletion while these programs are in the call stack (i.e., while they are running), a confirmation dialog like the one below will appear.

![Stop and Reset Confirmation Dialog](../_assets/stop_reset_dialog.png)

Pressing the `[ENTER]` key will stop and reset the corresponding task daemon. Pressing the `[ESC]` key will cancel the editing.

Additionally, if you delete a job program that is in the call stack, the corresponding daemon execution will be halted and initialized.
[__SOURCE](appendices/README.md)
# Attachment
[__SOURCE](appendices/rules-occupational-safety.md)
# Rules and Notices on Occupational Safety and Health Standards

The industrial robot in question must be installed considering the inspection criteria of the Rules and Notices on Occupational Safety and Health Standards (in case of inspection target).

"[Rules on Occupational Safety and Health Standards](https://hrbook-hrc.web.app/#/view/rules-on-occupational-safety-and-health-standards/korean/README)"
[__SOURCE](quality-assurance.md)
# Quality Assurance

"[Quality Assurance](https://hrbook-hrc.web.app/#/view/quality-assurance/korean/README)"