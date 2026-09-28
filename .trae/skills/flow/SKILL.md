---
name: flow
description: "会话与概览: 初始化 `.flow` 结构(`/flow-init`), 会话恢复定位当前进展(`/flow-resume`), 概览输出(`/flow-overview`)"
---

# Flow Skill

需求管道会话与概览. 概览与摘要均读写 `.flow/demand-pipeline.md`.

## 触发
- 斜杠命令: `/flow-init` `/flow-resume` `/flow-overview`
- 会话恢复不自动触发, 新会话开场 AI 不做主动扫描; 用户显式 `/flow-resume` 时才执行

## init 初始化
- 先读取 `.trae/rules/flow-core.md` 获取 UID/状态/格式等共享不变量, 再执行
- 创建 `.flow/` 骨架: README.md, STRUCTURE.md, demand-pipeline.md, task-track/0000.md, idea-track.md, task-track/layer2/ 与 layer3/(按需扩展 layer4+)
- `.flow/README.md` 一句话概述 + 链接(规则指向 skills 与 flow-core, 设计来源指向 docs/architecture/flow-design.md)
- 根文件 0000.md 模板: frontmatter(uid="0000", title=项目名, description) + 第一层 5-8 个生命周期主阶段条目, 每行状态 `.`, 子UID `-`, 如 需求分析/架构设计/MVP/DEMO/1.0/1.1
- 仅当用户显式要求一次性建立更深层(L2+)时, 才在 init 阶段扩展多层; UID 分配严格遵循 flow-core 的单一全局顺序(max+1), 严禁按层级/父级分段预留, 建立顺序即为分配顺序
- 幂等: 已存在文件跳过, 缺失文件补齐. 根文件 0000.md 已存在时, 不重排其 UID/子UID

## resume 会话恢复
1. 读根文件 0000.md, 按 SEQ 扫描跳过状态 `x`, 找第一个非 `x` 任务
2. 该任务有子UID 则打开子文件继续下钻, 重复至无子UID 的末梢节点
3. 读末梢文件 mode: `serial` → 该任务即下一步; `parallel` → 列出该文件内所有非 `x` 任务由用户选择
4. 当前层级全 `x` 时, 依 parent_file/parent_uid 回溯父层级, 继续按 SEQ 找下一个非 `x` 任务并下钻
5. 输出当前进展位置, 下一步建议, 双轨道概览

## overview 概览
- 输出 demand-pipeline.md 摘要: 当前任务链路, 任务/创意轨道概览, 最大UID, 最近会话摘要

## 会话摘要
- 每轮结束后覆盖式更新 demand-pipeline.md: 当前任务链路, 下一步建议, 概览计数, 最近会话摘要
- 不追加历史日志, 历史归档依赖 git 版本控制

设计来源: [flow-design.md](../../docs/architecture/flow-design.md)
