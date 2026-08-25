---
alwaysApply: false
description: Godot 4.x C#编码元约束;仅在代码生成,修改,重构时启用;普通知识问答不启用
---

> 优先使用内置Godot API,C#/.NET知识;不确定时查阅下方官方锚点文档

# 优先级(冲突按序号裁决,数字越小优先级越高)
1. Godot API约定(MUST)
2. C# 语言规范(MUST)
3. 本元规则文件(MUST) — 与 docs/CODING_STYLE.md 共同定义项目编码约束
4. `docs/CODING_STYLE.md`(SHOULD) — 项目特有编码风格;禁止与上面3项冲突,禁止重复官方已有规范

---

# 官方文档来源锚定(AI 不确定时查阅)

## Godot API
- A1 Godot C#基础: <https://docs.godotengine.org/en/stable/tutorials/scripting/c_sharp/c_sharp_basics.html>
- A2 API浏览器: <https://docs.godotengine.org/en/stable/classes/index.html>

## C#/.NET规范
- B1 C#语言参考: <https://learn.microsoft.com/dotnet/csharp/language-reference/>
- B2 .NET设计指南: <https://learn.microsoft.com/dotnet/standard/design-guidelines/>
