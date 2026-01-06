# 2.3. Editing jobs

The main job program or sub job programs of the task daemon can be edited freely. However, if you perform command insertion/deletion while these programs are in the call stack (i.e., while they are running), a confirmation dialog like the one below will appear.

![Stop and Reset Confirmation Dialog](../_assets/stop_reset_dialog.png)

Pressing the `[ENTER]` key will stop and reset the corresponding task daemon. Pressing the `[ESC]` key will cancel the editing.

Additionally, if you delete a job program that is in the call stack, the corresponding daemon execution will be halted and initialized.