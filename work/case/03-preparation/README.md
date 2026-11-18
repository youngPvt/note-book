# 执行准备典型问题

对应知识点：[base/03-preparation](../../base/03-preparation/README.md)。本目录按请求接入、执行准入和 Runtime 初始化组织准备阶段的问题，与任务执行案例分开。

## 问题入口

当前尚无独立整理的准备阶段故障案例。以下关联既有通用设计问题，便于按知识点回顾，不作为已发生故障或修复记录。

- 请求重发与重复受理：怎样查回原 Run、避免重复扣占额度和重复创建运行实例。[长任务可靠性](../02-connection/01-长任务可靠性.md) · [任务接入与运行创建](../../base/03-preparation/01-request-entry/README.md)
- 容量不足与执行归属：新任务满载拒绝，已受理任务故障后怎样保留原 Run、取得新执行权并接续。[长任务可靠性](../02-connection/01-长任务可靠性.md) · [执行准入与归属管理](../../base/03-preparation/02-scheduling/README.md)
- 初始化与上下文恢复：什么情况下需要重新准备实例，哪些状态必须恢复，哪些每轮输入可以重新组装。[重试与恢复的分工](../../base/03-preparation/03-runtime-setup/README.md#重试与上下文恢复的分工)

模型决策、Skill 激活和工具执行中的问题见 [任务执行案例](../04-execution/README.md)。

[返回案例总览](../README.md) · [对应知识模块](../../base/03-preparation/README.md)
