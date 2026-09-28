---
name: flow-idea
description: "创意轨道操作: 添加/更新/升格创意, 维护 `.flow/idea-track.md` 点子池; 触发方式 `/flow-idea add|update|promote` 或自然语言"
---

# Flow Idea Skill

创意轨道操作. 升格时先读取 `.trae/rules/flow-core.md`.

## 触发
- 斜杠入口: `/flow-idea`
- 子操作(加载后通过自然语言指定): `/flow-idea add` 添加 / `/flow-idea update` 更新 / `/flow-idea promote` 升格
- 自然语言: 如 "记录一个创意: 动态天气系统", "把创意 'xxx' 升格为任务"

## 表格格式
`| 序号 | 日期 | 标题 | 描述 | 标签 |` (固定 5 列)
- 序号: 自增数字, 当前最大序号 + 1; 删除/升格后该序号留空不补位, 新条目仍取当前最大值 + 1
- 序号仅用于创意轨道内定位, 不属于 UID 编号空间; 升格时废弃序号, 任务 UID 由 flow-core 生成流程从 demand-pipeline.md 最大UID 递增
- 日期: 当天日期 YYYY-MM-DD, AI 录入时自动填写
- 标签: AI 根据内容自动生成的初步分类, 逗号分隔

## 操作流程

### add 添加创意
- 在 idea-track.md 表格末尾追加一行: 序号 = 当前最大序号 + 1, 日期 = 当天, 标题/描述来自用户, 标签自动生成

### update 更新创意
- 按序号定位条目, 修改标题/描述/标签

### promote 升格为任务
1. 加载 flow-core
2. 委托 `/flow-task create` 全流程在任务轨道创建任务(分配 UID/SEQ, 写父引用, 概览构建刷新 demand-pipeline.md); 本 skill 不直接写 task-track 与 demand-pipeline.md(写路径单一入口, 见 flow-core)
3. 从 idea-track.md 删除该行, 序号留空不补位

设计来源: [flow-design.md](../../docs/architecture/flow-design.md)
