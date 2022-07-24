# 实验报告

## 简述功能
引入进程的概念，程序不再通过硬编码，而是通过加载elf数据的形式来执行。兼容LAB2 的基础上，增加 spawn 系统调用来创建新进程。

## 简答题
1. 不会执行 P1，因为 P2.pass = 250 + 10，存在 8bit 数溢出问题，p2.pass = 5，小于 p1.pass，依旧执行 p2。

2. `PASS_MAX – PASS_MIN <= BigStride / 2` 原因简述：因为优先级 prio >= 2，所以 stride.max <= BigStride / 2，任意两个 pass 间不会超过 stride.max


## 完成记录
1. 查看工程代码结构，在适当的位置添加LAB2 相关代码，内存分配和任务信息
2. 阅读 `fork` 和 `exec` 的代码，实现 `spawn`
