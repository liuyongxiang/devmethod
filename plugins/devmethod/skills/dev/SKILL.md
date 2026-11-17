---
name: dev
description: 在项目中落实已确认方案，编写实现与测试，并维护 specs、features、bugfix、实现偏差和开发交接记忆。任何改动代码的任务（含日常明确修复）都经本技能执行；新方案的设计与评审使用 design。
---

# dev

从已确认的设计或明确的开发任务接入，阅读项目入口文档（`AGENTS.md` / `Agent.md` / `CLAUDE.md`，
存在哪个读哪个，含其引用的规则文件）、当前代码、测试及相关 spec；
模块地图与项目红线见技术参考 `.agents/agent-docs/ROUTE.md`（存在时）。
先确定验收场景、实现边界与受影响契约，再按实际依赖安排开发。
普通修复无需被强制扩成完整设计流程；出现未确认的方案选择或方向级变化时，返回 `design`。

## 实现与开发记忆

1. 需要拆分开发单元、依赖或跟踪交付时，读取
   [feature-development-plan-template.md](references/feature-development-plan-template.md)。
   计划中的并行机会不是自动启动多代理的授权。
2. 实现与必要测试同步推进，运行与变更相关的行为测试，记录真实结果、未执行项及限制。
   工具链（构建 / lint / 类型检查 / 测试命令）按项目入口文档或 ROUTE.md 执行；
   **未约定时先询问用户，不臆造命令**。不运行与本次变更无关的全量检查或格式化工具，
   项目明令禁止的命令以入口文档红线为准。
3. 契约变化及时维护 `.agents/agent-docs/specs/<module>/`；写法见
   [spec-template.md](references/spec-template.md)。spec 描述当前有效约束，不积累进度日志。
   项目若有契约真相源（schema / DDL / 协议定义文件，位置见 ROUTE.md），契约变化必须同步维护，
   多份必须配套的真相源（如双份方言定义、开发与运行时双目录）**一份都不能漏**。
4. 设计是开发前资产；features 是开发落地后的实施与决策归档。一次连贯特性在
   `.agents/agent-docs/features/` 记录实际实现、实际决策及 why / why-not、设计偏差、验证和遗留；
   引用稳定设计标识与相关 spec，不复制整篇设计，不向设计回填实际结果。
   根因涉及契约或定位过程值得复用的修复，在 `.agents/agent-docs/bugfix/<module>/` 留存现象、根因和证据。
   可复制 [feature.md](assets/templates/feature.md) / [bugfix.md](assets/templates/bugfix.md)；详细结构见 [development-memory.md](references/development-memory.md)。
5. 读到文档与代码不一致时，核实后修订当前 spec / 记忆，明确设计意图、实际实现和验证结果。
   归档设计的方向不重写；偏差写在开发记录，方向变化交回设计阶段。

## 项目红线（存在 ROUTE.md 时必须守住）

ROUTE.md 列出的项目红线（安全边界、执行形态约束、同步纪律、凭据处理等）在开发中逐条核对；
红线判定标准与例外以项目入口文档为准。无 ROUTE.md 时以入口文档中的硬性规则为准。
红线相关修复值得复用时按 [防错模式提炼](references/defensive-patterns.md) 沉淀。

## 决策归属、行为证据与故障复用

- 记录实施前按 [决策归属与替代](../design/references/decision-records.md) 判断续写或新建，明确旧结论的效力；不重写历史验证事实。
- 按 [行为验证与证据](references/behavior-evidence.md) 选择实际入口、模型/用户可见结果和独立副作用验证；快照不能自己生成自己的正确性依据。
- 证据写入已有实施/故障记录，注明候选、环境、覆盖及重验条件；只重验受后续变化影响的范围，不把旧版本通过直接当成新版本通过。
- 有复用价值的根因按 [防错模式提炼](references/defensive-patterns.md) 更新所属规则/spec 与回归；不为普通修复机械新增模式文档。

按 [因果流程与资料可信度](references/context-and-flows.md) 选择上下文、追踪跨边界影响，
资料缺失时直接从源码和现有证据推进，不先建设全仓知识库。验证后同步本次影响的规则、契约和已有模块/流程资料，
交接明确盘点、理解、检视与验证各自的覆盖和仍待完成部分。

## 命名

- 新 feature：`F_YYYYMMDD_HHMM_<slug>.md`。
- 新 bugfix：`B_YYYYMMDD_HHMM_<slug>.md`。
- 时间使用 Asia/Shanghai，精确到分钟，创建后修订不改名。
- 完整标识包含主题；同一分钟不同主题可并行建档。重名时确认是否续写同一记录，否则细化主题，禁止覆盖。
- 历史编号和文件名不调整；spec 沿用已有编号规则。
- 每个事实保留一个维护位置，其余文档以标识或链接引用，避免复制当前契约与进度。

## 设计交接与归档

设计生命周期和字段复用 [lifecycle.md](../design/references/lifecycle.md)，不重复定义。
开始实际开发时将已批准设计移到 `designs/active/`，同步 `stage`、`status`、日期；
设计作者、评审人、状态与日期继续以设计 frontmatter 为唯一来源；计划及 features 引用该处。
随实现将版本、实现定位、实际决策、偏差、验证证据和遗留归档到 features，
不在设计中填写 `implementation` 或实际执行章节。设计中的验证计划只定义预期与通过条件。
目标方案需要变更时返回 design，经确认后按日期更新设计，不把意外实现自动写成已确认方案。
根据真实测试和交付结论更新状态；仅在交付得到确认后移入 `designs/archive/`。
迁移保留设计标识和附件，更新索引；索引命令见 design 技能。
feature 的写法还可参考 [feature-design-template.md](../design/references/feature-design-template.md)
中明确标注供开发阶段使用的证据、偏差与归档指导。

完成后交给检视阶段：变更范围、设计/spec 依据、测试证据、已知风险、未完成项。
需要转测文档时，将实际实现版本、范围和测试证据交给 [testing](../testing/SKILL.md)；
转测资料的撰写与状态维护由该技能负责，开发阶段继续维护实现和测试本身。

## 提交组织

提交遵循项目入口文档的提交约定；无约定时采用 Conventional Commits 并先与用户确认风格。
任何提交先向用户展示具体提交计划（提交位置、消息、包含范围）并取得确认；
不自动提交或创建 MR/PR；生成物或脚本单写的归档分支不手改。
提交前完成项目要求的增量检查（入口文档 / ROUTE.md 列明）。

## 文档元数据与索引

遵循 [作者、设计关联与索引规则](../design/references/document-metadata.md)：受管文档以 frontmatter 标注 Git author，移除 owner；新 F 平铺在 features/，F/B 显式声明 designs。
写作/修改后自动运行文档同步工具刷新索引；检视只报告缺口，不改写待审资料。
