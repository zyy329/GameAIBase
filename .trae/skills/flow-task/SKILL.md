---
name: flow-task
description: "任务轨道操作: 创建/更新/状态变更/删除/移动, 维护树状WBS任务文件与状态收敛; 触发方式 `/flow-task-create|update|status|delete|move` 或自然语言"
---

# Flow Task Skill

任务轨道操作. 执行任何操作前先读取 `.trae/rules/flow-core.md`.

## 触发
- 斜杠命令: `/flow-task-create` `/flow-task-update` `/flow-task-status` `/flow-task-delete` `/flow-task-move`
- 自然语言: 如 "创建一个任务: 实现登录功能", "把 0021 标记为进行中"

## 操作流程

### create 创建任务
1. 加载 flow-core
2. 读取目标父文件与 `.flow/demand-pipeline.md`
3. 新UID = 最大UID + 1, 4位补零; 覆盖更新 demand-pipeline.md 最大UID记录
4. 父文件表格末尾新增行: UID / 新SEQ / 标题 / `.` / 子UID(`-` 或子文件UID)
5. 有子任务时创建子文件于 layerN(父层级+1) 目录: 文件名 = UID.md, frontmatter 含 uid/parent_file/parent_uid/title/description
6. 覆盖更新 demand-pipeline.md 摘要(当前任务链路, 最大UID, 概览计数)

### update 更新任务
1. 按 UID 定位目标文件与行
2. 修改标题/描述/状态等字段
3. 更新 demand-pipeline.md 摘要

### status 状态变更
1. 按 UID 定位目标行, 修改状态符号 `x`/`>`/`.`
2. 触发状态收敛(flow-core: 自底向上 `x` 收敛, `>` 向上传播)
3. 覆盖更新 demand-pipeline.md 摘要并给出下一步建议

### delete 删除
- 叶子任务行: 直接删除, 重排当前文件 SEQ, UID 留空不复用
- 带子文件任务行: 禁止直接删除; 先处理子任务, 或逐层向用户确认后递归删除
- 更新 demand-pipeline.md 摘要与最大UID记录

### move 移动
- 跨父移动同步维护 4 处: 原父表删引用+重排, 目标父表新增行+分配新SEQ, 子文件更新 parent_file/parent_uid 并移动到对应 layerN 目录, 更新 demand-pipeline.md
- UID 不变

## 自动维护
- AI 判定任务完成后自动将该任务状态置 `x` 并触发状态收敛, 无需用户手动标记
- 每次状态变更后覆盖更新 demand-pipeline.md 摘要; 自动判定的变更在摘要中列清单供用户复核/回滚

## 会话摘要
- 输出本次完成与变更清单, 当前进展位置, 建议下一步, 并提醒用户切换新会话

设计来源: [flow-design.md](../../docs/architecture/flow-design.md)
