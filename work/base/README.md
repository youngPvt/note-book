# Agent 基座知识清单

本页是 `base` 的顶层知识入口。第一层按 `00`—`12` 划分总体架构和模块，第二层列出各模块的细知识点，第三层由模块 `README.md` 与独立专题展开内容。已有专题直接链接文件，其余知识点链接到模块内的对应章节。

知识范围覆盖 Agent 运行、模型调用和平台工程，不包含模型微调与训练。架构说明与技术示例保存在 `base`，复习安排、口述和案例整理放在独立的复习目录。

本方案采用 **Agent Runtime＋Skill＋工具**，不单独建设 Workflow 模块。Skill 描述任务方法，模型根据目标与反馈选择动作，Runtime 与工具服务落实状态管理、执行约束和结果校验。

实际问题与解决方案保存在 [case 案例目录](../case/README.md)，按相同模块编号组织。各模块 `README.md` 提供对应案例入口，已关联的细知识点也可直接跳转到具体案例。

## 00 总体架构与模块关系

- [整体架构与执行链路](./00-overview/README.md#总体架构)，企业助手、需求流水线与共享基座的关系。
- [Agent Runtime 与 Skill 的执行方案](./00-overview/README.md#agent-runtime-与-skill-的执行方案)，动态执行、Skill 方法复用、关键条件校验及运行状态机的边界。
- [管理、执行、记忆与公共组件的职责](./00-overview/README.md#四部分的职责与核心对象)，配置管理、运行治理及资源服务的边界。
- [统一运行对象](./00-overview/README.md#统一运行对象)，AgentVersion、Session、Run、Attempt 与业务关联。
- [一条需求中的模块协作](./00-overview/README.md#用一条需求串起所有模块)，从身份校验到执行结果与记忆提炼。
- [部署单元与存储分工](./00-overview/README.md#小规模部署建议)，管理服务、Worker、关系库、对象存储、索引与队列。
- [跨模块故障处理](./02-execution/13-wait-recovery/01-组件协作与故障处理.md)，工具响应丢失、版本回滚、归档失败、索引滞后与权限撤销。

## 01 管理层

- [租户、工作区与可信身份](./01-management/README.md#租户工作区与身份)，成员、服务账号、委托关系和资源隔离。
- [租户配置服务与执行权限](./01-management/02-租户配置服务与执行权限.md)，配置与治理职责、统一读取、可信身份传递及各模块实际校验。
- [配置版本与缓存更新](./01-management/02-租户配置服务与执行权限.md#配置版本与缓存更新)，固定 Agent 依赖、当前租户限制、变更通知与关键状态检查。
- [Agent 配置与版本发布](./01-management/README.md#agent-配置与发布)，依赖快照、发布校验、弃用、停用和回滚。
- [对外无版本与运行配置快照](./01-management/03-Agent配置发布与运行快照.md)，用户按 Agent 调用、后台发布版本、Run 快照、重试恢复及权限实时生效。
- [Skill 租户授权与 Agent 绑定](./01-management/README.md#skill-授权与-agent-绑定)，全局、租户自有及平台授权范围，与 Agent 启用版本的关系。
- [资源授权与人工审批](./01-management/README.md#权限与审批)，授权范围收窄、动作绑定、实时撤权和恢复复查。
- [配额、审计与管理事务](./01-management/README.md#配额审计与管理接口)，额度预占、用量结算、条件更新、outbox 和通知一致性。
- [Agent 权限与执行安全](./01-management/01-Agent权限与执行安全.md)，幻觉、提示注入、越权访问、审批与副作用的不同处理边界。

## 02 执行层与 Agent 行为

- [执行层 14 个环节总览](./02-execution/README.md#环节目录)，启动、循环、交付、等待恢复和全程观测，各环节均有独立目录。
- [Codex、Pi 与 DeepSeek Harness 参考](./02-execution/参考实现与设计边界.md)，公开机制、接入限制、源码版本及平台补充能力。
- [请求接入与入口门禁](./02-execution/01-request-entry/README.md)，输入校验、幂等、可信身份和 Run 快照。
- [调度与 Worker 领取](./02-execution/02-scheduling/README.md)，排队、配额、租约、Attempt 与执行所有权。
- [Runtime 与环境准备](./02-execution/03-runtime-setup/README.md)，适配器、会话、沙箱、工作区与工具入口就绪。
- [任务理解与动态计划](./02-execution/04-intent-planning/README.md)，意图判断、澄清、方法选择与计划修订。
- [能力发现与资料检索](./02-execution/05-capability-retrieval/README.md)，Skill、工具、知识、记忆和产物的按需获取。
- [上下文组装与预算检查](./02-execution/06-context-assembly/README.md)，来源、消息协议、Token 预算、裁剪与压缩。
- [模型调用](./02-execution/07-model-invocation/README.md)，请求适配、流式响应、结构化提议与调用错误。
- [动作前置门禁](./02-execution/08-pre-action-gate/README.md)，参数校验、实时授权、业务前置条件和审批。
- [工具执行](./02-execution/09-tool-execution/README.md)，MCP、API、脚本、并发、操作 ID 与副作用。
- [动作后置检查](./02-execution/10-post-action-check/README.md)，返回校验、不可信内容、错误分类与证据。
- [循环控制与任务汇聚](./02-execution/11-loop-control/README.md)，继续、重规划、停止、预算与子任务汇聚。
- [完成验收与交付](./02-execution/12-completion-delivery/README.md)，结果证据、产物持久化、终态、通知和资源回收。
- [等待、中断与恢复](./02-execution/13-wait-recovery/README.md)，人工输入、取消、重试与恢复一致性。
- [事件记录与全程观测](./02-execution/14-observability/README.md)，状态流、用量、审计与评测数据。

以下专题展开循环模式与通用运行契约：

- [Agent 本体与执行循环](./02-execution/11-loop-control/01-Agent本体与执行循环.md)，规划、决策、行动、反馈、状态与停止条件。
- [规划执行与反思模式](./02-execution/04-intent-planning/01-规划执行与反思模式.md)，ReAct、Plan-and-Execute、Reflection、外部证据与有限重规划。
- [多 Agent 协作与结果汇聚](./02-execution/11-loop-control/02-多Agent协作与结果汇聚.md)，角色、子 Run、上下文隔离、权限预算、共享修改与冲突处理。
- [运行对象与可信上下文](./02-execution/README.md#运行对象与可信上下文)，逻辑调用、Worker 尝试、固定版本与业务 Task 关联。
- [Agent 循环与 Runtime 适配](./02-execution/README.md#agent-循环与-runtime-适配)，Skill 加载、工具反馈、并发调用、人工等待及 LangGraph／SDK 能力差异。
- [Run 状态与调度所有权](./02-execution/README.md#run-状态与调度所有权)，队列、租约、fencing、并发控制、重试与预算。
- [副作用与运行恢复](./02-execution/README.md#外部副作用与恢复)，稳定操作 ID、外部对账、checkpoint 和工作区一致性，以及后续章节中的等待、取消与事件重连。

## 03 上下文管理与渐进式加载

- [资源可用与模型可见](./03-context/README.md#资源可用与模型可见)，注册、发现、挂载、读取和进入模型输入的区别。
- [上下文组装与资源加载职责](./03-context/README.md#职责与来源)，Context Assembler、ResourceLoader、来源清单和实时授权。
- [各类资源的渐进式加载](./03-context/README.md#各类资源怎样逐步加载)，工具定义、Skill、知识片段、产物和长期记忆。
- [加载循环与上下文预算](./03-context/README.md#加载循环与预算)，预加载、发现、范围读取、历史摘要、结果裁剪及输出空间。
- [SDK 上下文接入边界](./03-context/README.md#sdk-接入边界)，动态工具支持、内部历史可见性、撤权后的会话处理。

## 04 记忆分类与生命周期

- [Runtime 与记忆服务的分工](./04-memory/README.md#对象与职责)，当前工作状态、checkpoint、跨任务条目与上下文组装。
- [工作、情景、语义和程序性记忆](./04-memory/README.md#四类记忆如何使用)，内容、读写触发、验证状态、来源及版本引用。
- [记忆作用域与授权检索](./04-memory/README.md#作用域与检索)，租户、用户、项目、发布状态、缓存和召回过滤。
- [记忆条目与服务接口](./04-memory/README.md#字段与服务接口草案)，标识、来源、适用条件、保留期限和查询发布接口。
- [候选提炼、发布与并发控制](./04-memory/README.md#写入与并发处理)，幂等事件、事实冲突、CAS、生效版本与索引更新。
- [记忆撤销、删除与污染控制](./04-memory/README.md#撤销与污染控制)，来源失效、衍生事实、缓存清理、备份恢复与 SDK 会话重建。

## 05 模型调用、Prompt 与选型

- [模型目录与调用治理](./05-models/README.md#模型配置与运行治理)，Provider、ModelProfile、能力标签、凭证与 Runtime 适配。
- [Prompt 工程与结构化输出](./05-models/01-Prompt工程与结构化输出.md)，指令、材料、Few-shot、CoT、输出契约和模板版本。
- [结构化输出的检查层次](./05-models/01-Prompt工程与结构化输出.md#结构化输出要经过三道检查)，响应状态、schema、业务语义、资源权限及有限修复。
- [LLM 推理与模型选型](./05-models/02-LLM推理与模型选型.md)，Token、Prefill、Decode、KV Cache、连续批处理与生成参数。
- [推理性能与任务时延](./05-models/02-LLM推理与模型选型.md#性能指标要分层看)，TTFT、生成速度、排队和多轮工具链路开销。
- [业务效果比较与降级兼容](./05-models/02-LLM推理与模型选型.md#用业务任务比较候选模型)，固定样本、工具协议、历史续用和外部动作核对。
- [限流、重试、预算与用量](./05-models/README.md#模型配置与运行治理)，超时、费用口径、实际与估算用量，以及 SDK 内部调用的可控范围。

## 06 MCP 与企业工具

- [工具目录与版本契约](./06-mcp/README.md#工具目录与调用治理)，MCP／API 接入、工具 schema、版本快照与兼容性。
- [工具授权与执行检查](./06-mcp/README.md#工具目录与调用治理)，实际参数、资源范围、审批、HTTP 令牌受众与 stdio 身份。
- [调用异常与副作用核对](./06-mcp/README.md#工具目录与调用治理)，明确失败、结果不确定、幂等键、唯一约束与审计。
- [工具发现与按需 schema 装载](./06-mcp/README.md#渐进式加载边界)，客户端筛选、模型工具暴露和 SDK 动态工具能力。

## 07 Skill 与方法版本管理

- [Skill 全流程知识点拆解](./07-skills/04-Skill全流程知识点拆解.md)，选择、加载、执行、组合、状态恢复、权限、版本及效率评测八组细知识点。
- [Skill 包与依赖管理](./07-skills/README.md#注册发布与运行装载)，SKILL.md、参考资料、模板、脚本、内容摘要与环境依赖。
- [Skill 数据模型](./07-skills/02-Skill数据模型与资源依赖.md#skill-与-skillversion-分层)，身份层与版本层、recall、instructions、resources、dependencies 的字段和存储映射。
- [附属资源与能力依赖](./07-skills/02-Skill数据模型与资源依赖.md#resources-资源清单)，参考资料、脚本、模板的使用方式，以及后续章节中的 Skill 组合、工具引用和依赖校验。
- [Skill 验证、发布与回滚](./07-skills/README.md#注册发布与运行装载)，适用 Runtime、授权范围、评测记录和 AgentVersion 引用。
- [多租户 Skill 配置与存储](./07-skills/01-多租户Skill配置存储与运行时加载.md)，管理层与 Skill 服务分工、四张核心表、对象存储及发布链路。
- [大量 Skill 的检索与召回](./07-skills/01-多租户Skill配置存储与运行时加载.md#大量skill的检索与召回)，范围过滤、版本匹配、描述索引、候选选择与有限补搜。
- [Skill 加载到 Runtime](./07-skills/01-多租户Skill配置存储与运行时加载.md#runtime的按需加载)，search_skills、load_skill、工具结果进入上下文及附属资源按需读取。
- [Skill 渐进式装载](./07-skills/README.md#渐进式加载边界)，元数据发现、正文激活、附属资源读取和脚本执行。
- [内容缓存与上下文驻留](./07-skills/03-渐进式加载与缓存.md#内容缓存与上下文驻留)，服务接口、版本缓存、权限复查、上下文去重与压缩后重新加载。
- [长文本与加载性能](./07-skills/03-渐进式加载与缓存.md#长文本与运行时延)，远端读取、额外模型轮次、输入量、预取与正文预加载的区别。
- [程序性记忆与 Skill 的关系](./07-skills/README.md#注册发布与运行装载)，方法正文、适用条件、来源以及候选改进到发布版本的过程。

## 08 知识接入与 RAG 检索

- [知识源接入与索引生命周期](./08-knowledge/README.md#知识接入与检索)，同步、解析、切分、索引更新和失败进度。
- [授权召回与重排](./08-knowledge/README.md#知识接入与检索)，ACL 下推、隔离分区、候选选择、返回与缓存复核。
- [检索证据与数据新鲜度](./08-knowledge/README.md#知识接入与检索)，文档 ID、版本、片段位置、引用、删除和权限变更传播。
- [知识片段按需展开](./08-knowledge/README.md#渐进式加载边界)，问题触发检索、补充读取原文、内容预算和实时授权。
- [知识与长期记忆的边界](./08-knowledge/README.md#知识接入与检索)，源文档证据、跨任务经验及关联失效处理。

## 09 沙箱、工作区与执行隔离

- [环境模板与隔离机制](./09-sandbox/README.md#环境与工作区生命周期)，镜像、依赖、工具版本，以及容器与更强沙箱的适用边界。
- [资源、网络与凭证约束](./09-sandbox/README.md#环境与工作区生命周期)，CPU、内存、磁盘、进程、出口、可写目录和短期授权。
- [Attempt 工作区与恢复一致性](./09-sandbox/README.md#环境与工作区生命周期)，独立目录、旧进程隔离、仓库基线、文件快照和 Runtime 会话。
- [归档、回收与受控外部访问](./09-sandbox/README.md#环境与工作区生命周期)，必要产物保存、环境释放、凭证撤销及 Git／生产系统访问。

## 10 产物持久化与访问

- [产物元数据与不可变版本](./10-artifacts/README.md#元数据持久化与访问)，归属、Run／Attempt、内容摘要、对象版本和仓库基线。
- [上传提交与完整性校验](./10-artifacts/README.md#元数据持久化与访问)，UPLOADING／READY、大小与摘要检查、重复提交和结果引用。
- [产物授权、保留与删除](./10-artifacts/README.md#元数据持久化与访问)，受控下载、签名地址有效期、撤权延迟和衍生引用清理。
- [产物按需读取与验证证据](./10-artifacts/README.md#渐进式加载边界)，清单摘要、范围读取、工作区挂载、内容来源与 SDK 可见边界。

## 11 可观测性、审计与排障

- [Trace、Metric 与 Log 的关联](./11-observability/README.md#信号关联与留存)，Run、Attempt、执行步骤和模型／工具操作的关联标识。
- [运行指标与成本记录](./11-observability/README.md#信号关联与留存)，排队、完成、恢复、取消、工具时延及实际或估算用量。
- [上下文、记忆和环境诊断](./11-observability/README.md#信号关联与留存)，材料版本、裁剪、记忆撤销、沙箱资源与产物归档。
- [观测数据的权限与可靠性](./11-observability/README.md#信号关联与留存)，脱敏、采样、租户隔离、必要审计、遥测故障和不可见信息。

## 12 自动化评测与质量验证

- [Agent 自动化评测与效果量化](./12-evaluation/01-Agent自动化评测与效果量化.md)，执行过程、技术结果、业务验收及独立证据的分层。
- [样本、环境与版本固定](./12-evaluation/01-Agent自动化评测与效果量化.md#样本与版本)，场景覆盖、调试与保留样本、模型配置、Session 和记忆隔离。
- [规则、测试、人工与 LLM judge](./12-evaluation/01-Agent自动化评测与效果量化.md#评测器如何组合)，评价标准、证据引用、偏差检查与人工校准。
- [成功率、时延与成本口径](./12-evaluation/01-Agent自动化评测与效果量化.md#成功率与成本口径)，分母、失败样本、首次验收、人工修正和有效交付成本。
- [重复试验与上线回归](./12-evaluation/01-Agent自动化评测与效果量化.md#重复试验与上线回归)，独立运行、稳定性、逐样本比较、发布门槛及灰度回滚。

[复习计划、口述与案例整理](../review-plan/README.md)
