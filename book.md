
[__SOURCE](README.md)
# ${cont_model} 控制器功能手册 - 任务守护程序
[__SOURCE](0-about-this-manual/README.md)
# 关于手册

[__SOURCE](0-about-this-manual/precautions.md)
# 注意事项

{% include file="zh/precautions.md" %}
[__SOURCE](0-about-this-manual/safety-notice.md)
# 安全注意事项

{% include file="zh/safety-notice.md" %}

[__SOURCE](1-overview/README.md)
# 1. 概述
[__SOURCE](1-overview/1-prerequisite.md)
# 1.1 先决条件

要很好地理解本手册，您应该具备以下知识。

* [${cont_model} 机器人控制器操作手册](https://hrbook-hrc.web.app/#/view/doc-hi6-operation/zh-tp630/README?cont_model=${cont_model})
[__SOURCE](1-overview/2-about.md)
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
[__SOURCE](2-how-to-use/README.md)
# 2. 如何使用
[__SOURCE](2-how-to-use/1-setting.md)
# 2.1. 设置

选择 `[F2: 系统] - 4: 应用参数 - 15: 15：任务守护进程 ([F2: system] - 4: Application parameters - 15: Task daemon)`。

![任务守护进程菜单](../_assets/menu.png)

<br>

将打开如下所示的设置界面。  
可以为任务 1 到任务 7 配置设置。（任务 0 不能作为守护进程使用。）

- 如果您在 `作业编号 (job no.)` 字段中输入工作编号，该任务将设置为使用该编号作为主程序运行的守护进程。  
如果设置为 0，则该任务将不作为守护进程使用。换句话说，它处于守护进程关闭状态。

- 勾选 `自动执行 (Auto exe.)` 将在设置完成或控制器启动时自动执行守护进程。
- 勾选 `重复 (Repeat)` 将在作业完成后从头开始重复作业循环。这在概念上与将 `[F7: 条件设置] - 操作循环类型 ([F7: cond.set] - Operation cycle type)` 设置为 `连续 (Continuous)` 相同。

- `状态 (Status)` 字段显示任务的当前状态以及当前程序计数器（程序编号/步骤编号/功能编号）在括号中。

  - OFF: 不作为守护进程使用的状态。
  - OCCUPIED: 由于多任务而当前正在使用的任务。它不能作为守护进程使用。
  - READY: 等待从程序头部启动的状态。
  - RUN: 守护进程当前正在运行的状态。
  - STOP: 执行已停止的状态。
  - WAITING: 在 `延迟 (delay)` 语句、`wait` 语句、`input` 语句等处等待的状态。
  - ERROR: 发生错误的状态。可能还会显示错误代码。
  - END: 工作循环已完成的状态。

![任务守护进程设置界面](../_assets/setting.png)

可以使用底部的 F 键对当前选定的任务执行手动操作。

- `[F1: 重置] ([F1: Reset])`: 停止选择的任务并执行重置。这在概念上与执行 `R0,[ENTER]` 相同。所有调用信息和局部变量都被清除，程序计数器重置为主程序头位置。
- `[F2: 执行] ([F2: Execution])`: 启动处于 STOP、READY 或 END 状态的任务守护进程。这在概念上与按下 `START` 按钮相同。
- `[F3: 停止] ([F3: Stop])`: 停止处于 RUN 或 WAITING 状态的任务守护进程。这在概念上与按下 `STOP` 按钮相同。

- `[F7: 确认] ([F7: OK])`: 保存设置并关闭设置界面。设置为 `自动执行 (Auto exe.)` 的任务守护进程将开始执行。
[__SOURCE](2-how-to-use/2-monitoring.md)
# 2.2. 监控

![任务守护进程监控](../_assets/monitoring.png)

在 `窗格布局 - [F1: 选择] - 多任务 (pane layout - [F1: select] - multitask)` 监控窗口中，您可以检查任务守护进程的当前程序计数器。
[__SOURCE](2-how-to-use/3-job-editing.md)
# 2.3. 编辑任务

任务守护程序的主任务程序或子任务程序可以自由编辑。然而，如果在这些程序处于调用栈中（即，它们正在运行时）执行命令插入/删除，将会出现如下的确认对话框。

![停止和重置确认对话框](../_assets/stop_reset_dialog.png)

按下 `[ENTER]` 键将停止并重置相应的任务守护程序。按下 `[ESC]` 键将取消编辑。

此外，如果您删除一个在调用栈中的任务程序，相应的守护程序执行将被暂停并初始化。
[__SOURCE](appendices/README.md)
# 附件
[__SOURCE](appendices/rules-occupational-safety.md)
##### 职业安全与健康标准的规则和通知

所涉及的工业机器人必须在考虑《职业安全与健康标准的规则和通知》的检查标准的情况下进行安装（如为检查目标）。

"[职业安全与健康标准的规则](https://hrbook-hrc.web.app/#/view/rules-on-occupational-safety-and-health-standards/zh/README)"
[__SOURCE](quality-assurance.md)
# 质量保证

"[Quality Assurance](https://hrbook-hrc.web.app/#/view/quality-assurance/zh/README)"