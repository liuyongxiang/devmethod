# devmethod 编码路由规则（插件 SessionStart hook 自动注入）

- **编码开发必须走 devmethod**：凡是实现特性、修复缺陷、重构或补写测试等改动代码的任务，
  动手前先调用 `devmethod:coding-flow` 技能识别当前阶段，按其强制要求与阶段路由选择
  design / dev / review / testing 阶段技能执行，不得跳过直接改代码。
- 大特性或较大范围改动，先由 coding-flow 评估规模并询问用户是否进入 `devmethod:design`；
  未命中判定条件的日常明确修复直接使用 `devmethod:dev`。
- 项目尚无 `.agents/agent-docs/` 脚手架时，可运行 `/devmethod:init` 一次性创建后再继续流程。
