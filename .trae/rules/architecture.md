---
alwaysApply: false
description: 架构强约束;仅在代码生成,修改,重构时启用;普通知识问答不启用
---

# 架构强约束

> 详见 docs/architecture/architecture.md. 本文件仅列 AI 生成代码时必须遵守的硬规则

## 1. 分层
- System: 纯 C# 类, 不继承 Node, 可单测
- Node: Godot 节点, 实现 IView 接口, 只管显示与输入转发

## 2. 依赖方向
- System 持有 IView 接口, 禁止持有 Godot Node 类型
- System 禁止主动 GetNode
- Node 禁止直接改 System 内部状态, 只调 public API

## 3. 通信
- 跨场景: EventBus (同帧派发)
- 同场景跨节点: Godot Signal
- 父子节点内部: 直接方法 (豁免 Signal)

## 4. 依赖注入
- Pure DI, 手动构造函数注入, 禁用 DI 框架
- 组装根: scripts/autoload/Bootstrap.cs

## 5. 禁止
- 深继承 (层级 >= 3)
- 跨场景 GetNode
- 同场景内滥用 EventBus
