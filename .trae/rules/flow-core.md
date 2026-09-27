---
alwaysApply: false
description: 需求管道共享不变量(UID生成/SEQ重排/状态收敛/layerN目录判定/文件格式校验);flow-task与flow-idea在创建和维护任务时加载
---

# Demand Pipeline Core

需求管道(flow-task/flow-idea 轨道)共享不变量, 只在此处定义一处. flow-task 创建任务/子文件, flow-idea 升格创建任务前, 先读取本文件再执行.

## UID 生成
- 读 `.flow/demand-pipeline.md` 最大UID记录, 新UID = max + 1, 4位补零; 起自 `0001`, 根文件固定 `0000`; 超过 `9999` 自然进位为 5 位, 无上限
- 生成后覆盖更新 demand-pipeline.md 的最大UID记录
- UID 全局唯一, 永不复用, 不编码层级; 文件级与任务级 UID 共用同一编号空间
- 创意轨道 `序号` 不属于 UID 编号空间, 不参与 UID 计数

## SEQ 重排
- SEQ 仅当前文件内有效, 纯数字 1,2,3...; 插入/删除任务后重排当前文件 SEQ 列, 保持连续
- SEQ 不外溢到文件名与跨文件引用

## 状态收敛
- 状态符号仅三种: `x` 完成, `>` 进行中, `.` 待开始
- 自底向上收敛: 某任务所有子任务均为 `x` 时, 父文件对应引用行自动置 `x`, 逐层向上至根
- 进行中向上传播: 某任务状态置 `>` 且父引用行当前为 `.` 时, 父引用行同步置 `>`, 逐层向上至根
- 串行不强制: 模式仅作 AI 推荐顺序依据, 不禁止乱序推进, 不整改用户显式设置的状态

## layerN 目录判定
- 根文件 `0000.md` 位于 `.flow/task-track/`; 第 N 层文件位于 `.flow/task-track/layerN/`
- 子文件层级 = 父文件层级 + 1; 层级变化时同步移动到对应 layerN 目录

## 文件格式校验
- 文件名 = UID + `.md`, 不含描述文字
- YAML frontmatter: `uid`(必填), `parent_file`/`parent_uid`(非根必填, 构成定位链路), `mode`(`serial`/`parallel`, 默认 `serial`), `title`(必填), `description`(可选)
- 任务列表表格固定 5 列: `| UID | SEQ | 标题 | 状态 | 子UID |`
- `子UID` 列只允许 1 个 UID 或 `-`; 多子任务通过子文件内多行实现
- 跨文件引用统一使用 UID

## 删除与移动
- 叶子任务行可直接删除, 随后重排当前文件 SEQ; UID 留空不复用
- 带子文件的任务行(`子UID` 不为 `-`)禁止直接删除; 先处理子任务, 或逐层向用户确认后递归删除
- 删除最大UID 时记录保持原值, 不回落
- 跨父移动同步维护 4 处: 原父表删引用+重排, 目标父表新增行+分配 SEQ, 子文件更新 parent_file/parent_uid+移动目录, 更新 demand-pipeline.md
- 移动不改变 UID
