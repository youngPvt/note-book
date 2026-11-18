# 任务执行与 Agent 行为典型案例

对应知识点：[base/04-execution](../../base/04-execution/README.md)。目录编号和名称与知识模块保持一致。

## 本模块案例

- [Skill 激活集合与工具授权](./01-Skill激活集合与工具授权.md)：工具可见但调用被拒绝，以及上一请求的读取限制残留。

## 相关模块案例

- [执行准备问题入口](../03-preparation/README.md)：请求幂等、容量准入和 Runtime 初始化的相关问题。
- [长任务可靠性](../02-connection/01-长任务可靠性.md)：从连接接入延伸到执行准入、Worker 接管、外部操作核对与最终交付的通用设计问题。
- [Skill 正文传输不完整](../05-context/01-Skill正文被工具结果截断.md)：工具成功返回与下一次模型输入完整性的区别。

## 按任务执行知识点回顾

执行层按 [任务生命周期](../../base/04-execution/README.md#环节目录) 组织，模型规划、工具执行和结果反馈等知识点直接放在执行目录下，整体关系见 [执行循环总览](../../base/04-execution/执行循环总览.md)。已有案例按问题位置关联，不复制案例正文：

- [动作前置门禁](../../base/04-execution/05-pre-action-gate/README.md)：对应 Skill 激活集合与工具授权案例。
- [上下文组装](../../base/04-execution/01-context-assembly/README.md) 与 [动作后置检查](../../base/04-execution/07-post-action-check/README.md)：对应 Skill 正文传输不完整案例。

[返回案例总览](../README.md) · [对应知识模块](../../base/04-execution/README.md)
