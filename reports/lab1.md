# 实验报告
*2022-7-16*

## 简述功能
该实验实现了一次性加载多个用户程序，然后分时轮流执行多个程序，程序也可以主动让出CPU使用权。并实现了一个新的系统调试以获取人物运行信息：任务状态、系统调用使用次数、让你无运行总时长。

## 简答题
1. sbi版本和三个 bad 测试的报错信息如下：
```bash
[rustsbi] Implementation     : RustSBI-QEMU Version 0.1.1
...
[ERROR] [kernel] PageFault in application, bad addr = 0x0, bad instruction = 0x80400408, core dumped.
[ERROR] [kernel] IllegalInstruction in application, core dumped.
[ERROR] [kernel] IllegalInstruction in application, core dumped.
```
    `ch2b_bad_address.rs` 中访问了非法地址 `0`，导致了 PageFault；`ch2b_bad_instructions.rs` 中使用了非法指令 `sret`，这是 S 态的指令；`ch2b_bad_register.rs` 中访问了非法寄存器， S 态的状态寄存器 `sstatus`用户太程序无权访问。

2. 关于trap.S
    1. `a0` 是 `__restore` 的第一个参数，是内核栈压入Trap上下文之后的栈顶。使用 `__restore` 的情景1：运行用户程序；情景2：处理完Trap后返回 U 态；
    2. 在 `__alltraps` 时，将 `sstatus`, `sepc`, `sscratch` 三个寄存器分别存在了 `t0`, `t1`, `t2` 中，因此在 `__restore` 中需要现恢复这三个寄存器，再将通用寄存器恢复出来。 <br>`sstatus`: `SPP` 等字段给出 Trap 发生之前 CPU 处在哪个特权级（S/U）等信息； <br>`sepc`: 当 Trap 是一个异常的时候，记录 Trap 发生之前执行的最后一条指令的地址； <br>`sscratch`: 用户态栈顶；
    3. `x2` 即 `sp`，已做保存，`x4` 即 `tp` 除非手动出于一些特殊用途使用它，否则一般不会被用到；
    4. `sp` 指向内核栈，`sscratch` 指向用户栈；
    5. `sret` 指令后发生状态切换，能进入用户态是因为做了上下文恢复，并在该指令的前一条指令，交换了 `sp`与`sscratch`，即 `sp` 重新指令用户栈；
    6. `sp` 指向用户栈，`sscratch` 指向内核栈；
    7. `ecall`，触发名为 `Environment call from U-mode` 的异常， 并 Trap 进入 S 模式。

## 完成记录
1. 根据 `TaskInfo`，在 `TaskControlBlock` 中添加系统调用计数，任务起始时间
2. 在 `syscall` 中添加函数 `record_syscall` ，记录 `syscall_id` 对应的信息
3. `record_syscall` 调用 `TaskManager` 内部方法，实现调用次数加1
4. `sys_task_info` 中添加函数 `get_task_info`，获取 `TaskInfo`
5. `TaskInfo` 调用 `TaskManager` 内部方法，完成 `TaskInfo` 的更新


