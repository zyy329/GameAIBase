---
alwaysApply: false
description: 需求管道共享不变量(UID生成/SEQ重排/状态收敛/layerN目录判定/文件格式校验);flow-task与flow-idea在创建和维护任务时加载
---

# Demand Pipeline Core

需求管道(flow-task/flow-idea/flow-init)共享不变量, 只在此处定义一处. flow-task 创建任务/子文件, flow-idea 升格创建任务, flow-init 一次性建立多层骨架时, 均先读取本文件再执行.

## 写路径单一入口
- task-track 下所有文件与 `.flow/demand-pipeline.md` 的写操作, 唯一入口是 flow-task(create/update/status/delete/move/refresh); flow skill 对它们只读
- flow-idea promote 不直接写任务轨道与 demand-pipeline.md, 必须委托 flow-task create 全流程完成(含概览缓存刷新)
- 任何 task-track 写操作完成后, 必须立即按 flow-task 的概览构建流程(见 flow-task SKILL.md, 下钻算法唯一真相源)刷新 demand-pipeline.md 缓存, 保证缓存与树一致
- 例外: 用户手动编辑 task-track 或 git 切分支/合并后, 缓存可能过期, 由用户显式执行 `/flow-task refresh` 重建

## UID 生成
- 读 `.flow/demand-pipeline.md` 最大UID记录, 新UID = max + 1, 4位补零; 起自 `0001`, 根文件固定 `0000`; 超过 `9999` 自然进位为 5 位, 无上限
- 生成后覆盖更新 demand-pipeline.md 的最大UID记录
- UID 全局唯一, 永不复用, 不编码层级; 文件级与任务级 UID 共用同一全局编号空间, 顺序在同一单调递增序列中逐一分配
- **严禁按父/层级/位置预留整段 UID 块**. 不允许出现"父=0001 则其文件=0010", "L1 用 00xx, L2 用 01xx, L3 用 02xx" 等分层分段. 多层树建立时, 所有文件 UID 与任务 UID 全部取自同一个 max+1 序列, 逐项推进
- 正确示例(多层级连续分配): L1 行 0001,0002,..., L1 的子文件取 0006, 文件内任务行取 0007,0008,..., 后续层级继续 +1, 任一 UID 不体现其所在层级或父级
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
