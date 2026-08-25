---
alwaysApply: false
description: 编码规范一致性校验;仅在编辑 docs/CODING_STYLE.md 或 .trae/rules/coding-style*.md 时启用;普通知识问答不启用
---

# 一致性校验规则(修改 .trae/rules/coding-style.md 或 docs/CODING_STYLE.md 时必须执行)

## 1. 与官方规范的边界校验

新增/修改条目时, 条目与 Godot API(A1/A2) 或 C#/.NET 规范(B1/B2) 的关系:

1. **冲突**
    - 禁止直接写入文档
    - 输出报告: 列出 [冲突位置, 冲突内容, 对应官方文档来源], 等待用户确认调整
2. **重复** (条目已在官方规范中存在)
    - 禁止写入, 直接剔除重复项

> A1/A2/B1/B2 锚点见 `.trae/rules/coding-style.md`

## 2. 两份文档职责划分

- **`.trae/rules/coding-style.md`**: 仅存放元规则(优先级, 来源锚点); **不写具体项目编码风格**
- **docs/CODING_STYLE.md**: 仅存放项目特有编码约定(命名, 目录, 格式, 注释增补等); 必须通过边界校验, 不得与元规则冲突
- 若两份文档交叉/冲突: 输出差异位置清单, 提示用户统一文档

## 3. 新增/修改规则流程

1. 判断条目归属:
    - 属于 Godot/C# 官方规范 → 拒绝写入项目文档
    - 属于元规则/AI 易错强调项 → 写入 `.trae/rules/coding-style.md`
    - 属于项目业务编码风格 → 写入 `docs/CODING_STYLE.md`
2. 修改完成后, **重新执行 §1, §2 校验**
