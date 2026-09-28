---
name: flow-task
description: "任务轨道操作: 创建/更新/状态变更/删除/移动/刷新缓存, 维护树状WBS任务文件, 状态收敛与概览缓存; 下钻算法唯一真相源; 触发方式 `/flow-task create|update|status|delete|move|refresh` 或自然语言"
---

# Flow Task Skill

任务轨道操作. 执行任何操作前先读取 `.trae/rules/flow-core.md`.

## 触发
- 斜杠入口: `/flow-task`
- 子操作(加载后通过自然语言指定): `/flow-task create` 创建 / `/flow-task update` 更新 / `/flow-task status` 状态变更 / `/flow-task delete` 删除 / `/flow-task move` 移动 / `/flow-task refresh` 重建概览缓存
- 自然语言: 如 "创建一个任务: 实现登录功能", "把 0021 标记为进行中"

## 操作流程

### create 创建任务
1. 加载 flow-core
2. 读取目标父文件与 `.flow/demand-pipeline.md`
3. 新UID = 最大UID + 1, 4位补零; 覆盖更新 demand-pipeline.md 最大UID记录
4. 父文件表格末尾新增行: UID / 新SEQ / 标题 / `.` / 子UID(`-` 或子文件UID)
5. 有子任务时创建子文件于 layerN(父层级+1) 目录: 文件名 = UID.md, frontmatter 含 uid/parent_file/parent_uid/title/description
6. 执行概览构建, 刷新 demand-pipeline.md

### update 更新任务
1. 按 UID 定位目标文件与行
2. 修改标题/描述/状态等字段
3. 执行概览构建, 刷新 demand-pipeline.md

### status 状态变更
1. 按 UID 定位目标行, 修改状态符号 `x`/`>`/`.`
2. 触发状态收敛(flow-core: 自底向上 `x` 收敛, `>` 向上传播)
3. 执行概览构建, 刷新 demand-pipeline.md

### delete 删除
- 叶子任务行: 直接删除, 重排当前文件 SEQ, UID 留空不复用
- 带子文件任务行: 禁止直接删除; 先处理子任务, 或逐层向用户确认后递归删除
- 最大UID记录: 删除的是最大值时保持原值不回落; 执行概览构建, 刷新 demand-pipeline.md

### move 移动
- 跨父移动同步维护 4 处: 原父表删引用+重排, 目标父表新增行+分配新SEQ, 子文件更新 parent_file/parent_uid 并移动到对应 layerN 目录, 执行概览构建刷新 demand-pipeline.md
- UID 不变

### refresh 重建概览缓存
- 触发时机: 用户手动编辑过 task-track, 或 git 切分支/合并后, 显式执行
- 不改动任务树, 仅重新执行概览构建, 整体覆盖 demand-pipeline.md 的 当前任务 与 概览 两 section

## 概览构建(下钻算法, 唯一真相源)

下钻算法只在此处定义; flow skill 的 resume 与 `.flow/README.md` 只消费或链接本流程, 不复述算法. 每次写操作后及 refresh 时执行, 产物覆盖写入 demand-pipeline.md.

1. 从根文件 `0000.md` 按 SEQ 扫描, 跳过状态 `x`, 找第一个非 `x` 任务行
2. 该行 `子UID` 不为 `-` 则打开对应子文件继续下钻, 重复直至无子UID 的末梢节点
3. 读末梢所在文件的 mode:
   - `serial`: 末梢任务即下一步
   - `parallel`: 列出该文件内所有非 `x` 任务作为并行候选, 交用户选择
4. 当前层级任务全为 `x` 时, 依 parent_file/parent_uid 回溯父层级, 按 SEQ 找下一个非 `x` 任务并继续下钻; 记录回溯后的下一链路
5. 最大UID: 读 demand-pipeline.md 现有记录(写操作时 +1 更新, 见 UID 生成规则)

### demand-pipeline.md 缓存字段

- 当前任务链: 根 `0000` 省略, 非根层级 UID 以 `-` 连接, 一直列到末梢节点; 括号附层级标题
- 下一步建议: serial 时格式 `推进 UID 标题 (状态符号)`; 括号内状态符号供 resume 末梢校验
- 并行候选: 仅 mode=parallel 输出, 逐行列出该文件全部非 `x` 任务(UID 标题 状态符号)
- 回溯下一链路: 仅步骤 4 触发回溯时输出, 格式同当前任务链
- 最大UID: 从 demand-pipeline.md 概览 section 读取, 写操作时更新为新值; 不参与下钻计算, 仅作 UID 生成依赖

## 自动维护
- AI 判定任务完成后自动将该任务状态置 `x` 并触发状态收敛, 无需用户手动标记
- 每次状态变更后执行概览构建刷新 demand-pipeline.md; 自动判定的变更在最近会话摘要中列清单供用户复核/回滚, 删除类高风险动作再次提示

## 会话摘要(最近会话摘要 section 的唯一写入口)
- demand-pipeline.md 的 最近会话摘要 section 仅由 flow-task 在每轮会话结束后覆盖式重写(不追加历史日志, 归档依赖 git); flow skill 对该 section 只读
- 内容: 本次完成项与自动变更清单, 当前进展位置, 下次起点, 提醒切换新会话

设计来源: [flow-design.md](../../docs/architecture/flow-design.md)
