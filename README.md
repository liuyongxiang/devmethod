# devmethod

**五阶段 AI coding 开发方法论插件**：把「需求澄清 → 设计 → 开发 → 检视 → 转测」做成 Claude Code 可调用的技能，
配套 D/F/B/S 四类受管文档（设计方案 / 实施归档 / 故障档案 / 当前规约）与自动索引工具链，
任何项目装上即用，项目差异经一份 `ROUTE.md` 技术参考适配。

## 五个技能

装好后以「插件名:技能名」调用（如 `/devmethod:design`）。**自动触发不靠模型自觉**：
插件自带 `SessionStart` hook，装上即把「改动代码必经 coding-flow 路由」的强制规则注入每个会话上下文，项目侧零配置。

| 调用名 | 阶段 | 干什么 |
|---|---|---|
| `devmethod:coding-flow` | 全流程路由 | 识别当前阶段并调度其余四技能；任何改代码任务强制经过 `dev`，大特性先问用户是否进 `design` |
| `devmethod:design` | 设计 | 方案形成、评审、生命周期管理（proposals→active→archive），产出可交给开发的已确认设计 |
| `devmethod:dev` | 开发 | 落实已确认方案：实现与测试、spec/feature/bugfix 开发记忆维护、提交计划确认 |
| `devmethod:review` | 检视 | 基线→候选差异分析、契约与红线核对、P0–P3 问题输出与复查 |
| `devmethod:testing` | 转测 | 把开发交接整理成测试人员可独立执行、结果可追溯的转测文档 |

## 安装

本仓同时是一个插件市场（marketplace）。从本地路径安装：

```bash
claude plugin marketplace add /Users/fei/IdeaProjects/DataIntell/devmethod
claude plugin install devmethod@devmethod
```

或从 GitCode 远程安装（私有仓，需仓库访问权限与可用的 git 凭据）：

```bash
claude plugin marketplace add https://gitcode.com/qq_22408539/devmethod.git
claude plugin install devmethod@devmethod
```

安装后重启会话生效；marketplace 名固定为 `devmethod`（取自 `marketplace.json`，与仓库名无关）。
插件升级后需重装一次让缓存快照刷新（本地源改动 → `claude plugin install devmethod@devmethod` 重跑即可）。

## 项目接入（装插件后两步，均可选）

1. **建文档树脚手架**（在项目根运行一次 `/devmethod:init`，或手动）：

   ```bash
   mkdir -p .agents/agent-docs/designs/{proposals,active,archive,testing} \
            .agents/agent-docs/{specs,features,bugfix}
   ```

   `.agents/` 入库与否由项目自行决定：团队共享就提交跟踪，个人本地就加进忽略清单。
   不建也不阻塞流程——技能首次需要落档时可按 dev 阶段约定现建。
2. **写 ROUTE.md**：`/devmethod:init` 会按 review 技能的唯一模板
   [route.md](plugins/devmethod/skills/review/assets/templates/route.md)
   生成 `.agents/agent-docs/ROUTE.md` 最小骨架，基于骨架补齐实际内容
   （仓库定位、模块地图、红线、验证命令）；规范与维护纪律见
   [project-route.md](plugins/devmethod/skills/review/references/project-route.md)。
   可选但强烈建议——没有它，技能退化为「读入口文档 + 实际代码」，模块词表与红线逐次口头确认。

强制路由规则（「改代码必须走 coding-flow / dev」）无需再写进入口文档——由插件 SessionStart hook
在每个会话自动注入，装上即生效。

技能读取的项目锚点就两个：**入口文档**（`AGENTS.md` / `Agent.md` / `CLAUDE.md`，存在哪个读哪个）与
**ROUTE.md**；工具链命令未知时技能会先询问用户，不臆造。

## 文档体系与工具链

`.agents/agent-docs/` 下四类受管文档，均带 frontmatter、由脚本自动生成索引：

- `designs/`（D）：`D-YYYYMMDD-HHMM-<slug>/design.md`，含生命周期与按日期评审记录
- `features/`（F）：`F_YYYYMMDD_HHMM_<slug>.md` 平铺，记录实际实现、决策与 why-not
- `bugfix/`（B）：`bugfix/<module>/B_YYYYMMDD_HHMM_<slug>.md`，现象/根因/回归证据
- `specs/`（S）：`specs/<module>/S_NN_<slug>.md`，当前有效契约

索引工具随 `design` 技能附带在 `scripts/`（从项目根运行；依赖 PyYAML，项目环境没有就用
`uv run --no-project --with pyyaml python …`）：

```bash
python "${CLAUDE_PLUGIN_ROOT}/skills/design/scripts/sync_documents.py"            # 全量同步元数据与索引
python "${CLAUDE_PLUGIN_ROOT}/skills/design/scripts/sync_documents.py" --document <新建文档路径>   # 建档时署名
python "${CLAUDE_PLUGIN_ROOT}/skills/design/scripts/validate_frontmatter.py"      # 可选校验
python "${CLAUDE_PLUGIN_ROOT}/skills/design/scripts/generate_index.py" --root .agents/agent-docs/designs
```

`${CLAUDE_PLUGIN_ROOT}` 未设置时（非插件上下文），用 `claude plugin list` 定位插件安装目录替换。
脚本自带 14 个单元测试（`plugins/devmethod/skills/design/tests/`）。

## 仓库结构

```
LICENSE                                # Apache 2.0
.claude-plugin/marketplace.json        # 插件市场清单（单插件）
plugins/devmethod/
├── .claude-plugin/plugin.json         # 插件清单
├── hooks/
│   ├── hooks.json                     # SessionStart hook：每会话自动注入强制路由规则
│   └── rules.md                       # 被注入的规则文本
├── commands/
│   └── init.md                        # /devmethod:init：一键建 .agents/agent-docs 脚手架
└── skills/
    ├── coding-flow/SKILL.md           # 阶段路由
    ├── design/                        # 设计技能 + references + templates + scripts + tests
    ├── dev/                           # 开发技能 + references + templates
    ├── review/                        # 检视技能 + references（含 project-route.md 规范）
    └── testing/                       # 转测技能 + references + templates
```

## 版本

- 0.2.0（2026-11-17）：新增 SessionStart hook 安装即自动注入强制路由规则；新增 `/devmethod:init` 脚手架命令；coding-flow / dev 技能触发描述增强；采用 Apache 2.0 协议（LICENSE + plugin.json SPDX 字段）。
- 0.1.0（2026-11-05）：从 DataSwarm 本地方法论插件化，通用化项目适配层（ROUTE.md），14 项工具链单测通过。

## 许可证

[Apache License 2.0](LICENSE)：可自由安装、使用、修改与分发本插件，须保留原版权与许可声明。
