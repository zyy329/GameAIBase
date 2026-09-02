---
alwaysApply: false
description: Godot 4 C# 引擎实践强约束(partial, Signal, 异步, 集合, 性能);仅在生成,修改 Godot C# 代码时启用;普通知识问答不启用
---

# Godot C# 实践强约束

> 来源: CCGS(godot-csharp-specialist/godot-specialist)提炼. 分层与通信见 architecture.md. 不确定的 API 先查 docs/engine-reference/godot/

## 1. 源生成器
- Node 派生类必须 `partial`, 否则源生成器静默失败
- Signal 委托名必须 `EventHandler` 后缀: `[Signal] public delegate void DiedEventHandler();`
- 触发用 `EmitSignal(SignalName.Died)`

## 2. 节点访问
- 必须 `GetNode<T>()` 泛型, 禁止无类型 `GetNode`

## 3. Signal 连接
- 优先 `+=` 订阅, `_ExitTree()` 中 `-=` 退订, 防 use-after-free
- 一次性事件用 `ConnectFlags.OneShot`
- Signal 仅用于向上通知(子到父), 同步请求响应用方法

## 4. 异步
- 等待引擎信号用 `await ToSignal(...)`, 禁止 `Task.Delay`(脱离主循环节拍)
- await 后检查 `IsInstanceValid(this)`(节点可能已释放)
- `async void` 仅限信号回调

## 5. 集合与资源
- C# 内部逻辑用 `List<T>`/`Dictionary<K,V>`, `Godot.Collections.*` 仅限跨语言或 `[Export]` 边界
- 自定义数据资源: `[GlobalClass] partial class XxxData : Resource` + `[Export]` 属性
- Resource 默认共享, 每实例数据需 `.Duplicate()`

## 6. 性能
- 空闲节点 `SetProcess(false)`/`SetPhysicsProcess(false)`
- 高频字符串比较用 `StringName`
- 热路径(`_Process`, 碰撞回调)禁止 LINQ 与每帧分配
- 高频生成对象(弹丸, 粒子)用对象池
- `_Process(double delta)` 参与引擎数学时转 `(float)delta`

## 7. 高频反模式(出现即修复)
- Node 类漏 `partial`
- `Task.Delay` 代替 `ToSignal`
- 无泛型 `GetNode`
- 忘记 `_ExitTree` 退订信号
- 静态字段持有 Node 引用(破坏场景重载)
- 手动调用 `_Ready()` 等生命周期方法
