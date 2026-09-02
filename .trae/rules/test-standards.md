---
alwaysApply: false
description: 测试代码强约束(命名, AAA, 隔离, 回归);仅在生成,修改 tests/ 下代码时启用;普通知识问答不启用
---

# 测试强约束

## 1. 命名
- `Test_[System]_[场景]_[预期结果]`, 禁止无语义命名

## 2. 结构
- 每测试 Arrange/Act/Assert 三段
- 断言必须精确到具体值, 禁止宽泛 bool

## 3. 隔离
- 单测禁止依赖外部状态(文件, 网络, 数据库)
- 外部依赖用 mock, IView 接口可 mock 是其设计动机之一
- 测试数据内联或专用 fixture, 禁止共享可变状态

## 4. 回归
- 每个 bug 修复必须附带能复现原 bug 的回归测试
