# 1.2 About the Task Daemon Function

{% hint style="info" %}
This feature is supported from V60.30-00 and later versions.
{% endhint %}

Generally, the job program of the Hi6 controller is executed only in automatic mode or when the StepFWD is pressed in manual mode.

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