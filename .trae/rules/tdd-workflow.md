---
alwaysApply: false
description: TDD 流程强约束(RED-GREEN-Refactor);仅在生成/修改 scripts/logic/ 下代码时启用;普通知识问答不启用
---

# TDD 流程强约束

> 纯逻辑代码 = scripts/logic/ 下不依赖 Godot 运行时的 C# 类(System, Model, IView 接口).
> 测试代码规范见 test-standards.md. 架构约束见 architecture.md.

## 1. 适用范围
- scripts/logic/ 下所有纯 C# 类(systems/, views/ 等)
- 禁止对 scripts/logic/ 外的代码做 TDD(autoload/, components/ 依赖 Godot)

## 2. 三步流程(强制顺序)
1. RED: 先写失败测试, 运行 `dotnet test` 确认失败
2. GREEN: 写最小实现让测试转绿, 禁止超前实现
3. REFACTOR: 重构实现, 测试保持绿
- 禁止先实现后补测试, 禁止跳过 RED 阶段

## 3. 测试位置与命名
- 路径镜像源码: scripts/logic/systems/Foo.cs -> tests/logic/systems/Test_Foo_*.cs
- 命名规范见 test-standards.md 第 1 节
- AAA 结构见 test-standards.md 第 2 节

## 4. 运行
- `dotnet test` (脱离 Godot 编辑器, 纯 .NET 运行)
- CI 中每次提交必跑

## 5. designos 联动(可选)
- 复杂 System 先在 designos/02-assumptions/ 登记 ASM-*.json
- 记录"为什么需要这个 System", 让测试用例意图可追溯

## 6. 回归
- bug 修复必附能复现原 bug 的测试(test-standards.md 第 4 节)
- 回归测试命名: Test_[System]_[Bug场景]_Fix
