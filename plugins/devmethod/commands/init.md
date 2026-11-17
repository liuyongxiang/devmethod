---
description: 为当前项目创建 devmethod 文档脚手架（.agents/agent-docs 目录树与可选 ROUTE.md 模板）
---

# init

为当前项目一次性建立 devmethod 文档基础设施。只做以下事情，完成后汇报创建/跳过结果：

1. 在项目根创建目录树（已存在的目录跳过，不覆盖任何已有文件）：

   ```bash
   mkdir -p .agents/agent-docs/designs/{proposals,active,archive,testing} \
            .agents/agent-docs/{specs,features,bugfix}
   ```

2. 若 `.agents/agent-docs/ROUTE.md` 不存在，按 review 技能的
   [project-route.md](../skills/review/references/project-route.md) 规范生成模板，
   包含仓库定位、模块地图、项目红线、验证命令四个小节的占位结构，并提示用户补齐实际内容。
3. 提醒用户：`.agents/` 入库与否由项目自行决定（团队共享就提交跟踪，个人本地就加进忽略清单）；
   本命令不执行任何 git 操作，也不修改任何入口文档。

ROUTE.md 的实际内容由用户确认后生效，不替用户做技术判断。
