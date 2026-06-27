
[__SOURCE](README.md)
# ${cont_model} 控制器功能手册 - 任务守护进程
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
# 1.1 前提条件

要很好地理解本手册，您应该具备以下知识。

* [${cont_model} 机器人控制器操作手册](https://hrbook-hrc.web.app/#/view/doc-hi6-operation/zh-tp630/README?cont_model=${cont_model})
[__SOURCE](1-overview/2-about.md)
# 1.2 关于任务守护进程功能

{% hint style="info" %}
该功能从 V60.30-00 及更高版本开始支持。
{% endhint %}

一般来说，${cont_model} 控制器的作业程序仅在自动模式下执行，或在手动模式下按下 StepFWD 时执行。

然而，有时可能需要在不满足这些播放条件的情况下在后台运行作业程序。 
例如，如果将报告控制器当前状态的网络服务功能实现为作业，那么让该作业始终运行，无论前述播放条件如何，都是有利的。

通过利用任务守护进程功能，您可以将特定作业分配给所需任务，并确保在播放条件下持续运行。

{% hint style="info" %}
守护进程指的是一个在后台持续运行的程序，通常处理来自外部系统的服务请求。
{% endhint %}

{% hint style="warning" %}

任务守护进程功能有以下限制：

- 像 `移动 (move)` 这样的步进命令被忽略而没有错误。换句话说，机器人或辅助轴不能移动。

- 使用多任务功能的任务 0 或任务编号不能作为守护进程使用。

- 大多数命令，例如下面列出的一些命令不起作用。

```python
cowork, axisctrl, filter, brake_check, gasp_check, softxyz, fctrl, softjoint, toolchng, load_esti, 等...
```

- 大多数机器人应用命令不起作用。

```python
arcon, lvs, multipass, cv.wait, heightsen, 等...
```

- 编辑作为任务守护进程运行的作业可能在某些情况下会停止该任务的守护进程执行。

{% endhint %}
[__SOURCE](2-how-to-use/README.md)
# 2. 如何使用
[__SOURCE](2-how-to-use/1-setting.md)
# 2.1. 设置

选择 `[F2: 系统] - 4: 应用参数 - 15: 任务后台执行 ([F2: system] - 4: Application parameters - 15: Task daemon)`.

![Task Daemon Menu](../_assets/menu.png)

<br>

将打开如下所示的设置屏幕。  
可以为任务 1 到任务 7 进行配置。 (任务 0 不能作为后台执行。)

- 如果在 `Job 编号 (job no.)` 字段中输入作业编号，该任务将以该编号作为主程序设置为后台执行。  
如果设置为 0，则该任务将不被用作后台执行。换句话说，它处于后台 OFF 状态。

- 检查 `自动执行 (Auto exe.)` 将在设置完成或控制器启动时自动执行后台程序。
- 检查 `重复 (Repeat)` 将在作业完成后从头开始重复作业 CYCLE。这在概念上与将 `[F7: 条件设置] - 运转周期 ([F7: cond.set] - Operation cycle type)` 设置为 `连续 (Continuous)` 是相同的。

- `状态 (Status)` 字段显示任务的当前状态，并在括号中显示当前程序计数器 (程序编号/步骤编号/功能编号)。

  - OFF: 不作为后台执行的状态。
  - OCCUPIED: 由于多任务而当前正在使用的任务。不能作为后台执行。
  - READY: 等待从程序头部启动的状态。
  - RUN: 后台程序当前正在运行的状态。
  - STOP: 执行已停止的状态。
  - WAITING: 在 `时间延迟 (delay)` 语句、`等待 (wait)` 语句、`input` 语句等处等待的状态。
  - ERROR: 发生错误的状态。错误代码也可以显示。
  - END: 作业 CYCLE 完成的状态。

![Task Daemon Settings Screen](../_assets/setting.png)

可以使用底部的 F 键对当前选择的任务执行手动操作。

- `[F1: 重置] ([F1: Reset])`: 停止所选任务并执行重置。这在概念上与执行 `R0,[ENTER]` 相同。所有调用信息和局部变量将被清除，程序计数器将重置为主程序头位置。
- `[F2: 执行] ([F2: Execution])`: 启动处于 STOP、READY 或 END 状态的任务后台程序。这在概念上与按下 `START` 按钮相同。
- `[F3: 停止] ([F3: Stop])`: 停止处于 RUN 或 WAITING 状态的任务后台程序。这在概念上与按下 `STOP` 按钮相同。

- `[F7: 确定] ([F7: OK])`: 保存设置并关闭设置屏幕。设置为 `自动执行 (Auto exe.)` 的任务后台程序将开始执行。
[__SOURCE](2-how-to-use/2-monitoring.md)
# 2.2. 监控

![Task daemon monitoring](../_assets/monitoring.png)

在`窗口调整 - [F1: 选择] - 多任务 (pane layout - [F1: select] - multitask)`监控窗口中，您可以查看任务守护程序的当前程序计数器。
[__SOURCE](2-how-to-use/3-job-editing.md)
# 2.3. 编辑任务

任务守护进程的主作业程序或子作业程序可以自由编辑。然而，如果在这些程序处于调用栈中（即正在运行时）执行命令插入/删除，则会出现如下确认对话框。

![停止和重置确认对话框](../_assets/stop_reset_dialog.png)

按 `[ENTER]` 键将停止并重置相应的任务守护进程。按 `[ESC]` 键将取消编辑。

此外，如果您删除一个在调用栈中的作业程序，相应的守护进程执行将被暂停并初始化。
[__SOURCE](appendices/README.md)
# 附件
[__SOURCE](appendices/rules-occupational-safety.md)
# 职业安全与健康标准的规则和通知

相关的工业机器人必须在考虑职业安全与健康标准的检查标准的情况下进行安装（如为检查目标）。

"[职业安全与健康标准规则](https://hrbook-hrc.web.app/#/view/rules-on-occupational-safety-and-health-standards/zh/README)"
[__SOURCE](quality-assurance.md)
# 质量保证

"[质量保证](https://hrbook-hrc.web.app/#/view/quality-assurance/zh/README)"