# Flow

需求管道 workspace: 任务轨道(树状WBS)与创意轨道(点子池)的双轨流转, 支持会话恢复与进度概览.

## task-track 组织

task-track 以 [0000.md](task-track/0000.md) 为根节点(项目生命周期主阶段), 每个任务行经 `子UID` 引用唯一子文件递归成树, 子文件以 parent_file/parent_uid 回溯. 定位当前进展需从根节点逐层下钻, 该下钻算法是 flow-task 概览构建的一部分, 规则不在本文件复述: [flow-task SKILL.md](../.trae/skills/flow-task/SKILL.md).

## 导航

- 规则: 见 skills `flow`, `flow-task`, `flow-idea` 与 `.trae/rules/flow-core.md`
- 设计来源: [docs/architecture/flow-design.md](../docs/architecture/flow-design.md)
- 顶层摘要: [demand-pipeline.md](demand-pipeline.md)
- 目录结构: [STRUCTURE.md](STRUCTURE.md)
