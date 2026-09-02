---
alwaysApply: false
description: 设计文档(GDD)结构与内容硬规则;仅在生成,修改 docs/design/ 下文档时启用;普通知识问答不启用
---

# 设计文档强约束

## 1. 必备 8 节
Overview, Player Fantasy, Detailed Rules, Formulas, Edge Cases, Dependencies, Tuning Knobs, Acceptance Criteria

## 2. 内容硬规则
- 公式必须含变量定义, 取值范围, 示例计算
- 边界情况必须写明确行为, 禁止"妥善处理"式含糊
- 依赖必须双向(A 依赖 B, B 文档须提及 A)
- 调参项须给安全范围及影响面
- 验收标准必须可测(QA 可判定 pass/fail)
- 平衡数值须链接来源公式或依据
- 禁止"手感好"类不可验证描述

## 3. 写作方式
- 先骨架后逐节填充, 每节经人工确认再写入
- 模板: docs/templates/game-design-document.md
