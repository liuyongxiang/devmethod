---
name: design
description: 在项目中分析需求、制定架构或特性方案、编写与评审设计文档，形成可交给开发阶段的已确认方案。适用于设计阶段；实现、测试和开发记忆维护交给 dev。
---

# design

设计资产根目录为项目根 `.agents/agent-docs/designs/`。
先阅读项目入口文档（`AGENTS.md` / `Agent.md` / `CLAUDE.md`，存在哪个读哪个）、相关模块的代码、测试、spec 和已有设计；
模块地图与项目红线见技术参考 `.agents/agent-docs/ROUTE.md`（按
[project-route.md](../review/references/project-route.md) 规范维护，不存在时以实际代码为准）。
区分当前行为、目标方案与待确认假设。

## 工作流程

1. 从具体场景说明问题、目标、范围和非目标，确认影响的模块及契约。
2. 根据任务选择文档，不强制每次都写齐所有类型：
   - 整体定位与工作链路：[overview-template.md](references/overview-template.md)。
   - 模块职责、协作与系统约束：[architecture-template.md](references/architecture-template.md)。
   - 一次行为变更的详细方案与取舍：[feature-design-template.md](references/feature-design-template.md)。
3. 需要独立评审与阶段跟踪的设计，复制唯一模板 [design.md](assets/templates/design.md)。
   它整合了设计管理字段、详细方案与日期评审记录；reference 仅提供指导，不再包含第二套可复制正文。
   作者、评审人、状态、日期等元数据统一保留在设计 frontmatter。
4. 评估主要备选方案，说明选择依据、失败路径、兼容与迁移影响、验证方案及未决问题。
   成稿时按 [writing-guide.md](references/writing-guide.md) 核对表达与事实边界。
5. 根据反馈迭代；只有用户或实际评审者明确确认后才记录批准。不得自行填写审批结论或评审人。
6. 交接已确认的目标、设计标识、受影响契约、验收场景、待决问题给 `dev`。
   用户只要求设计时，在交接处停止，不自动进入实现。

## 决策维护与验证交接

新建或修订方案前按 [决策归属与替代](references/decision-records.md) 查找已有决定，
说明本次是续写、独立新决定还是替代；真实备选及代价据实记录，不为套模板虚构方案。
验收条件覆盖受影响的模型/用户可见结果、实际入口与副作用；验证选择参考
[行为证据](../dev/references/behavior-evidence.md)，此阶段只定义预期与通过条件，实际执行归开发记录。
设计交接同时指出旧决定仍有效的范围及需核实的消费者。

对跨边界方案按 [因果流程与资料可信度](../dev/references/context-and-flows.md) 核对产生方、状态权威、载体、消费/恢复与最终结果。
既有项目地图只作导航；方案依据区分已证实、推断与待核实，明确未追通的关键关系，不以设计稿完整替代实现或验证证据。

## 命名与生命周期

- 新设计目录及 `id`：`D-YYYYMMDD-HHMM-<slug>`，例如 `D-20261104-1530-message-routing`。
  时间使用 Asia/Shanghai，精确到分钟；slug 为小写英文短横线主题，属于完整标识的一部分。
  完整名称存在时，先辨认是同一设计的续写还是不同设计；不同设计使用更具体的 slug，不覆盖、不分配递增号。
- 每目录含 `design.md` 和可选 `assets/`，初始位置是 `designs/proposals/`。
- `modules` 填相关模块标识列表（词表由项目 ROUTE.md 或入口文档约定，如 `[orchestration, web]`）；
  跨仓模块保留仓库前缀。INDEX 按模块分组，模块内展示生命周期。
  跨模块可在多个分组检索，缺失时进入"未分类"；`domain` 表示领域标识（项目自定义，不设全局白名单），不代替模块。
- 本阶段管理 `draft → in-review ⇄ changes-requested → approved / rejected`。
  评审与设计修订按实际日期追加记录并更新 `updated`，不维护递增 `version`。
- 设计文档是开发前的方案资产，仅包含设计与验证计划。实际实施、决策、偏差、验证结果和遗留
  由 [dev](../dev/SKILL.md) 归档至 features，不回填设计正文或 `implementation` 字段。
- 阶段、状态及字段见 [lifecycle.md](references/lifecycle.md)；开发阶段可维护设计的迁移与归档管理字段，
  目标方案变化需返回设计阶段确认后按日期修订。
- 草稿和被否决设计不是生产实现依据；已归档设计属于历史证据，当前行为仍须核对代码和 specs。
  按稳定标识引用设计，通过索引定位当前路径。

## 索引工具

索引与同步脚本随本技能附带在 `scripts/` 下，从**项目根目录**运行（脚本按当前目录定位 `.agents/agent-docs`，也可用 `--root` 显式指定）；仅依赖 PyYAML，用项目 Python 环境执行：

```bash
python "${CLAUDE_PLUGIN_ROOT}/skills/design/scripts/generate_index.py" --root .agents/agent-docs/designs
```

`${CLAUDE_PLUGIN_ROOT}` 未设置时（非插件上下文运行），先用 `claude plugin list` 定位插件安装目录再替换该变量；
`design/references/document-metadata.md` 中的 `sync_documents.py` 同理。
`validate_frontmatter.py` 为可选校验工具，不作流程门禁。
遵循项目入口文档的提交与权限约定，设计确认不授权提交或创建 MR/PR。

## 文档元数据与索引

遵循 [作者、设计关联与索引规则](references/document-metadata.md)：受管文档以 frontmatter 标注 Git author，移除 owner；新 F 平铺在 features/，F/B 显式声明 designs。
写作/修改后自动运行文档同步工具刷新索引；检视只报告缺口，不改写待审资料。
