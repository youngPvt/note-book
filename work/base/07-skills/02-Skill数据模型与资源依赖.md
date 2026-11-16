# Skill 数据模型与资源依赖

Skill 保存能力的身份与归属，SkillVersion 保存某个版本的召回信息、执行正文、附属资源和依赖声明。运行时可以根据召回信息选择方法，也可以直接加载任务入口或用户已选定的绑定版本，再按当前步骤读取资源或加载其他 Skill。

以下四部分是本平台的数据模型。Agent Skills 规范定义了必需的 `SKILL.md`，以及可选的参考资料、脚本和其他文件；`recall`、资源清单与依赖字段是平台为检索、发布和运行管理增加的结构。[Agent Skills 规范](https://agentskills.io/specification)

## Skill 与 SkillVersion 分层

| 层次 | 保存内容 | 更新方式 |
| --- | --- | --- |
| Skill | ID、展示名称、所有者、租户归属、可用范围、状态 | 由管理入口维护，范围与状态变更影响后续访问 |
| SkillVersion | 召回信息、正文入口、资源清单、依赖清单、包地址与摘要 | 发布后内容固定，修改内容生成新版本；发布或停用状态单独管理 |

SkillVersion 内部拆成四部分：

| 部分 | 主要字段 | 用途 |
| --- | --- | --- |
| `recall` | `name`、`description`、`tags`、`use_cases` | 描述能做什么、何时使用，供检索和候选选择 |
| `instructions` | 正文入口、格式，以及从包中解析的操作说明 | 承载整个任务的方法、步骤、判断条件、约束和资源使用时机 |
| `resources` | 类型、包内相对路径、用途，可附大小和内容摘要 | 声明参考资料、脚本、模板等包内文件 |
| `dependencies` | 所需 Skill 的固定版本、工具的版本引用 | 声明需要组合使用的其他能力 |

这四部分可以映射到现有 `skill_version` 表：召回信息使用已有结构化字段，增加 `instruction_entry`、`resource_manifest` 和 `dependency_manifest` 等字段即可；清单初期可采用 JSON。需要频繁按依赖关系查询时再拆子表，不必为四部分分别建设服务。

## 召回信息与执行正文

`recall` 是供检索使用的描述数据，不要求单独训练召回模型。名称、描述与标签进入索引，索引按 SkillVersion 构建；候选结果返回对应版本的描述，正文在选中后读取。

包内 `SKILL.md` 同时包含 YAML 元数据和 Markdown 正文。发布时从元数据解析 `name`、`description`，平台可从扩展元数据或管理配置中整理标签与适用场景，再固定到该版本。变更描述和标签也生成新版本，防止旧 Agent 用新的召回描述选择旧内容。

正文以版本包中的 `SKILL.md` 为内容来源。数据库保存入口与解析结果时，需要关联同一包摘要；索引、解析结果和缓存都从版本包及其发布元数据生成，不形成可独立编辑的另一份正文。

正文不仅告诉模型做什么，还应说明什么时候读取资源。例如经营报告 Skill 可以写明：

```markdown
1. 确认报告月份与数据范围。
2. 计算经营指标前，读取 references/metrics.md，采用其中的口径。
3. 需要图表时，加载已声明的数据图表 Skill，使用绑定的版本。
4. 使用 scripts/build_report.py 和 assets/report.xlsx 生成报告。
5. 核对报告数值、时间范围与来源数据。
```

这些步骤由 Agent 在 Runtime 驱动的循环中推进；Runtime 保存状态并处理恢复、预算和停止，工具服务检查权限及关键业务前置条件。读取正文不会自动运行其中提到的脚本。

## resources 资源清单

`resources` 是附属资源的总称，`references` 只是参考资料这一类。Skill 可以没有附属资源，也可以按需要带上不同类型的文件。

```text
monthly-business-report/
├── SKILL.md
├── references/
│   └── metrics.md
├── scripts/
│   └── build_report.py
└── assets/
    └── report.xlsx
```

| 平台资源类型 | 常见内容 | 运行时如何使用 |
| --- | --- | --- |
| `reference` | 指标口径、领域规则、长示例、操作细节 | 通过文件或资源读取工具获取必要内容，作为工具结果进入上下文 |
| `script` | 数据处理、校验、文件生成代码 | 下载或挂载到沙箱，通过已有执行工具运行，返回结果；源码无需全部进入上下文 |
| `template` | 报告、表格、文档模板 | 提供给文件生成工具，必要时读取结构 |
| `asset` | 图片、静态数据及其他辅助文件 | 由对应工具按格式使用 |

这些类型是平台分类，目录名称是包的组织约定。可以根据需要增加类型，但应有对应的读取或执行能力。将一个文件放进目录，不会自动为 Runtime 增加新工具。

资源路径相对于当前 Skill 版本的包根目录解析，读取接口限制在对应包范围内。资源随包版本固定；需要持续更新的业务数据通过已有数据工具或知识服务获取，正文说明如何使用其结果。

## dependencies 能力依赖

依赖声明用于引用独立维护的能力。经营报告中的指标说明是包内资源；可被多个任务使用的图表生成方法，可以登记为另一个 Skill，再通过依赖引用。

| 依赖对象 | 保存内容 | 使用方式 |
| --- | --- | --- |
| 其他 Skill | `skill_id` 与 `skill_version_id` | 执行到需要的步骤时，通过 `load_skill` 加载该版本正文 |
| 工具 | 平台登记的工具 ID 与版本引用 | Runtime 暴露受支持的调用入口，模型按步骤调用 |

依赖声明说明需要什么，不授予权限，不自动转换为执行 DAG，也不会自动创建子 Agent。使用顺序与条件由 Skill 方法说明和当前任务状态共同确定，Agent 结合工具反馈推进。当前方案由同一个 Agent 按需加载其他 Skill；需要独立子任务执行时，再采用执行层的多 Agent 机制。

## 版本数据示例

下面展示版本记录的逻辑结构。`instructions.entry` 指向包内正文，实际加载时返回解析后的正文内容；存储地址和摘要等字段在此省略。

```json
{
  "skill_id": "sk_monthly_report",
  "skill_version_id": "sv_report_003",
  "recall": {
    "name": "monthly-business-report",
    "description": "按约定经营口径分析数据并生成月度报告",
    "tags": ["经营分析", "月报"],
    "use_cases": ["生成月度经营报告"]
  },
  "instructions": {
    "entry": "SKILL.md",
    "format": "markdown"
  },
  "resources": [
    {"type": "reference", "path": "references/metrics.md"},
    {"type": "script", "path": "scripts/build_report.py"},
    {"type": "template", "path": "assets/report.xlsx"}
  ],
  "dependencies": {
    "skills": [
      {"skill_id": "sk_chart", "skill_version_id": "sv_chart_002"}
    ],
    "tools": [
      {"tool_id": "query_business_data", "tool_version": "v1"}
    ]
  }
}
```

## 发布与依赖校验

发布 SkillVersion 时，校验正文、资源路径和引用对象，并固定依赖版本。发布 AgentVersion 时继续展开依赖链，确认被引用的 Skill 和工具处于租户可用范围，并已纳入管理员确认的 Agent 配置；缺少绑定时提示补齐，不在运行时自动扩大能力范围。

沿用当前方案中“同一个 AgentVersion 对同一 Skill 只绑定一个版本”的规则。父 Skill 要求的版本与已有绑定冲突时，发布阶段提示调整依赖，不在运行时任意选择版本。所有被引用的 SkillVersion 随 AgentVersion 的绑定快照固定，加载时不临时取最新版。

首版可以拒绝循环依赖，并对依赖深度、单次运行的加载次数和上下文预算设置上限。发布时校验引用关系，执行时仍按当前步骤加载需要的正文和资源，不因为声明了依赖就一次性注入所有内容。

## 对应案例

- [跨行 YAML 描述解析不完整](../../case/07-skills/01-召回与元数据.md#案例二-跨行-yaml-描述被截断)
- [非当前必需资源缺失](../../case/07-skills/02-资源缺失与按需读取.md)

[返回 Skill 模块](./README.md) · [多租户配置与存储](./01-多租户Skill配置存储与运行时加载.md) · [渐进式加载与缓存](./03-渐进式加载与缓存.md) · [返回知识清单](../README.md)
