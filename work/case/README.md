# 典型问题与解决方案

案例目录与 `base` 的 `00`—`13` 模块保持相同编号和名称。每个模块以 `README.md` 列出案例，具体问题保存在独立文件中，记录原因、修复方式、验证范围和代码证据。知识模块与案例模块可以双向跳转。

跨模块问题按主要故障环节归档，在其他相关模块提供链接。新增案例放入对应模块，暂未整理案例的模块保留入口。

## 知识点目录

| 对应模块 | 基座知识 | 本模块案例 |
| --- | --- | --- |
| [00-overview](./00-overview/README.md) | [总体架构与模块关系](../base/00-overview/README.md) | 暂无已整理案例 |
| [01-management](./01-management/README.md) | [管理层](../base/01-management/README.md) | 暂无独立案例；模块入口包含相关案例链接 |
| [02-connection](./02-connection/README.md) | [连接接入与会话协作](../base/02-connection/README.md) | 暂无真实故障案例；入口列出问题分类与设计验收场景 |
| [03-execution](./03-execution/README.md) | [执行层与 Agent 行为](../base/03-execution/README.md) | [Skill 激活集合与工具授权](./03-execution/01-Skill激活集合与工具授权.md) |
| [04-context](./04-context/README.md) | [上下文管理与渐进式加载](../base/04-context/README.md) | [Skill 正文被工具结果截断](./04-context/01-Skill正文被工具结果截断.md) |
| [05-memory](./05-memory/README.md) | [记忆分类与生命周期](../base/05-memory/README.md) | 暂无已整理案例 |
| [06-models](./06-models/README.md) | [模型调用与 Prompt](../base/06-models/README.md) | 暂无已整理案例 |
| [07-mcp](./07-mcp/README.md) | [MCP 与企业工具](../base/07-mcp/README.md) | 暂无独立案例；模块入口包含相关案例链接 |
| [08-skills](./08-skills/README.md) | [Skill 与方法版本管理](../base/08-skills/README.md) | [典型问题总纲](./08-skills/00-Skill典型问题总纲.md)；[召回与目录元数据](./08-skills/01-召回与元数据.md)；[资源缺失与按需读取](./08-skills/02-资源缺失与按需读取.md) |
| [09-knowledge](./09-knowledge/README.md) | [知识接入与 RAG](../base/09-knowledge/README.md) | 暂无已整理案例 |
| [10-sandbox](./10-sandbox/README.md) | [沙箱与工作区](../base/10-sandbox/README.md) | 暂无已整理案例 |
| [11-artifacts](./11-artifacts/README.md) | [产物生成与持久化](../base/11-artifacts/README.md) | [Excel 生成内容丢失](./11-artifacts/01-Excel生成内容丢失.md) |
| [12-observability](./12-observability/README.md) | [可观测性与排障](../base/12-observability/README.md) | 暂无已整理案例 |
| [13-evaluation](./13-evaluation/README.md) | [自动化评测与质量验证](../base/13-evaluation/README.md) | 暂无独立案例；模块入口包含相关案例链接 |

现有案例主归属：召回与资源放在 `08-skills`，正文传输放在 `04-context`，激活集合与守卫放在 `03-execution`，生成内容完整性放在 `11-artifacts`。

[Skill 典型问题总纲](./08-skills/00-Skill典型问题总纲.md) 另外收录结合官方资料归纳的通用问题，不属于下方六个已有故障案例的复验结论。

## 本批案例的来源与验证范围

记录日期为 2026 年 11 月 15 日。本批六个故障案例及一个待改进项，依据提供的 Nexus 与 Tool Market 当前分支排查摘要整理。摘要未提供分支名、Commit、完整命令或逐项测试结果，不据此补写个人贡献、线上指标或全量测试结论。

原始证据路径位于 `/Users/yuanchuan/Documents/work/carys-nexus` 与 `/Users/yuanchuan/Documents/work/tool-market`。当前电脑不存在这两个目录，正文保留原路径及行号，供回到源仓库时定位；这些外部链接在当前电脑无法打开。本次完成的是案例整理与文档检查，未读取源代码或运行源项目测试。下表中的“原排查本轮”指提供者描述的复跑。

| 案例或环节 | 原记录中的验证结论 | 本次保留的边界 |
| --- | --- | --- |
| 中文召回与目录 | dev 同类请求正确调用灵策 MCP；原排查本轮复跑目录重点用例 | 不推断所有中文查询均可命中，也不推断大目录问题已解决 |
| Tool Market 元数据解析 | 有回归用例；原排查本轮复跑 Tool Market 重点用例 | 未附逐项结果，保留“已复跑”的范围 |
| Skill 正文传输 | 依据现行代码和仓库已有验证记录 | 原排查本轮因本机 `x-coder` 版本不匹配，未复跑正文传输套件 |
| 托管资源缺失 | 历史原包离线复测已能完成准备 | 不等同完整业务任务成功；原排查本轮同样因 `x-coder` 版本不匹配，未复跑托管资源套件 |
| 运行时作用域 | 已激活集合与请求读取范围两项 dev 复验通过；原排查本轮复跑工具激活重点用例 | 区分 dev 明确结论与未附明细的本地复跑 |
| Excel 内容保留 | 有生成脚本回归用例；原排查本轮复跑 Excel 生成重点用例 | 未附逐项结果，不扩展到所有格式或所有 Skill |
| 大目录按名称裁剪 | 超预算时相关 Skill 仍可能未展开 | 仍待改进；按问题召回是后续方案，不记为已完成修复 |

## 问题定位顺序

一次 Skill 任务可以分别检查：召回元数据是否完整、候选是否包含相关 Skill、正文是否实际进入模型输入、当前资源是否可读、工具展示与权限判定是否一致、生成结果是否完整。

候选选对只说明召回阶段满足条件，后续任一环节仍可能独立失败。正文、资源、授权和产物的问题分别修复、分别验证，不能用一次召回成功替代整条链路验收。

后续新增案例继续采用“典型问题 → 原因 → 解决方案 → 验证与证据”的结构。原修复事实与补充验证建议分别标注，待改进项在取得修复和验证记录后再更新状态。

[返回 work 总览](../README.md) · [基座知识清单](../base/README.md)
