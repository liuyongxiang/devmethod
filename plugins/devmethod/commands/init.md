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

2. 若 `.agents/agent-docs/ROUTE.md` 不存在，复制 review 技能的唯一模板
   [route.md](../skills/review/assets/templates/route.md) 到该路径，生成最小骨架；
   将 `〈项目名〉`（项目根目录名或入口文档中的名称）与 `〈YYYY-MM-DD〉`（当天日期）替换为实际值，
   其余 `〈占位内容〉` 原样保留，并提示用户按
   [project-route.md](../skills/review/references/project-route.md) 规范补齐实际内容。
3. 提醒用户：`.agents/` 入库与否由项目自行决定（团队共享就提交跟踪，个人本地就加进忽略清单）；
   本命令不执行任何 git 操作，也不修改任何入口文档。

ROUTE.md 的实际内容由用户确认后生效，不替用户做技术判断。
