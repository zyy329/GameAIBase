---
name: flow
description: "会话入口: 初始化 .flow 结构(`/flow init`), 会话恢复只读输出概览缓存与最近会话摘要(`/flow resume`); 概览构建与写入归 flow-task"
---

# Flow Skill

需求管道会话入口. 对 task-track 与 demand-pipeline.md 只读; 概览缓存的构建与写入归 flow-task.

## 触发
- 斜杠入口: `/flow`
- 子操作(加载后通过自然语言指定): `/flow init` 初始化 / `/flow resume` 会话恢复 / `/flow stat` 概览统计
- 会话恢复不自动触发, 新会话开场 AI 不做主动扫描; 用户显式要求时才执行

## init 初始化
- 先读取 `.trae/rules/flow-core.md` 获取 UID/状态/格式等共享不变量, 再执行
- 创建 `.flow/` 骨架: README.md, STRUCTURE.md, demand-pipeline.md, task-track/0000.md, idea-track.md, task-track/layer2/ 与 layer3/(按需扩展 layer4+)
- `.flow/README.md` 一句话概述 + 链接(规则指向 skills 与 flow-core, 设计来源指向 docs/architecture/flow-design.md)
- 根文件 0000.md 模板: frontmatter(uid="0000", title=项目名, description) + 第一层 5-8 个生命周期主阶段条目, 每行状态 `.`, 子UID `-`, 如 需求分析/架构设计/MVP/DEMO/1.0/1.1
- 仅当用户显式要求一次性建立更深层(L2+)时, 才在 init 阶段扩展多层; UID 分配严格遵循 flow-core 的单一全局顺序(max+1), 严禁按层级/父级分段预留, 建立顺序即为分配顺序
- 幂等: 已存在文件跳过, 缺失文件补齐. 根文件 0000.md 已存在时, 不重排其 UID/子UID

## resume 会话恢复(只读)
resume 不做任何下钻计算, 不写任何文件; 下钻算法的唯一真相源是 flow-task 的概览构建(见 flow-task SKILL.md).

1. 只读 `.flow/demand-pipeline.md`, 取 当前任务, 概览, 最近会话摘要 三 section
2. 末梢校验(防手动编辑/git 切分支导致缓存过期):
   - 取当前任务链末梢 UID, 打开其所在任务文件, 核对该行状态符号与缓存记录(下一步建议括号内符号; parallel 核对并行候选各行; 触发回溯时核对回溯下一链路末梢)
   - 一致: 继续; 不一致: 不输出可能错误的进展, 提示 "缓存可能已过期, 请执行 `/flow-task refresh` 后重试"
3. 只读输出:
   - 当前进展位置(当前任务链)
   - 下一步建议: serial 直接建议末梢任务; parallel 列出缓存中的并行候选由用户选择; 有回溯下一链路时一并说明
   - 最近会话摘要(原文只读输出, 不改写)

## stat 概览统计(实时扫描, 不持久化)

用户显式要求时执行(如"统计一下进度", "看看整体情况"). 实时扫描 task-track 和 idea-track, 只输出不写入 demand-pipeline.md.

### 扫描与输出
1. 加载 flow-core.md 确认 UID/状态/层级规则
2. 扫描根文件 `0000.md` 统计 L1 条目及 `x`/`>`/`.` 分布
3. 递归扫描所有 layerN 子文件, 可选择输出各层级累计或只展示 L1
4. 扫描 `idea-track.md` 统计暂存创意条数
5. 输出格式:
   - `任务轨道: L1 共 N 项 | x:n >:n .:n`
   - 可选: `各层级明细: L2 共 N 项, L3 共 N 项 ...`
   - `创意轨道: 暂存 N 条`

### 边界
- stat 是实时计算, 不写任何文件, 不修改 demand-pipeline.md
- L1 计数字段与 demand-pipeline.md 概览 section 曾有过的旧缓存格式相同, 但 stat 是按需计算, 不再持久化
- 不参与 UID 生成或下钻算法, 纯展示

## 概览与会话摘要的归属
- 概览缓存(当前任务链/下一步建议/并行候选/回溯下一链路/最大UID)由 flow-task 在每次写操作后经概览构建写入, `/flow` 不提供独立 overview 子操作(与 resume 只读输出重复)
- 概览统计(L1 计数/创意轨道条数等)由 `/flow stat` 实时扫描计算, 不持久化
- 最近会话摘要 section 由 flow-task 每轮会话结束覆盖式重写, resume 仅只读输出

设计来源: [flow-design.md](../../docs/architecture/flow-design.md)
