# 1.2 关于任务守护进程功能

{% hint style="info" %}
此功能自 V60.30-00 及更高版本开始支持。
{% endhint %}

一般而言，${cont_model} 控制器的作业程序仅在自动模式下执行，或在手动模式下按下 StepFWD 时执行。

然而，可能会出现即使在未满足这些播放条件的情况下，也需要在后台运行作业程序的情况。例如，如果实现一个网络服务功能以外部报告控制器的当前状态作为作业，则无论上述播放条件如何，让此作业始终运行将会是有益的。

通过利用任务守护进程功能，您可以将特定作业分配给所需的任务，并确保它始终持续运行，无论播放条件如何。

{% hint style="info" %}
守护进程是指在后台持续运行的程序，通常处理来自外部系统的服务请求。
{% endhint %}

{% hint style="warning" %}

TaskDaemon 功能有以下限制：

- 步骤命令如 `移动 (move)` 被忽略而没有错误。换句话说，机器人或辅助轴无法移动。

- 使用多任务功能的任务 0 或任务编号不能用作守护进程。

- 大多数命令如下所列，不会起作用。

```python
cowork, axisctrl, filter, brake_check, gasp_check, softxyz, fctrl, softjoint, toolchng, load_esti, etc...
```

- 大多数机器人应用程序命令不会起作用。

```python
arcon, lvs, multipass, cv.wait, heightsen, etc...
```

- 编辑作为 TaskDaemon 运行的作业在某些情况下可能会停止该任务的守护进程执行。

{% endhint %}