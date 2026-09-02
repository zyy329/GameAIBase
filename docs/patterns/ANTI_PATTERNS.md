# 反面示例 (Anti-Patterns)

> 列出"不要这样做"的反面示例. AI 生成代码时应避免

---

## 一, System 继承 Node

### 反例

```csharp
public class CombatSystem : Node
{
    public override void _Process(double delta) { /* ... */ }
}
```

### 问题

- System 与 Godot 节点耦合, 无法脱离场景树测试
- 违反"逻辑层纯 C#"边界

### 正例

```csharp
public class CombatSystem
{
    private readonly IGameClock _clock;
    public CombatSystem(IGameClock clock) { _clock = clock; }
    public void Tick(float dt) { /* ... */ }
}
```

---

## 二, System 主动 GetNode

### 反例

```csharp
public class PlayerSystem
{
    public void Move()
    {
        var node = GetTree().Root.GetNode<PlayerNode>("Game/Player");
        node.Position += new Vector2(10, 0);
    }
}
```

### 问题

- System 反向耦合场景树结构
- 场景结构调整即破坏 System

### 正例

```csharp
public class PlayerSystem
{
    private readonly IPlayerView _view;
    public PlayerSystem(IPlayerView view) { _view = view; }
    public void Move(float dx) { _view.SetPosition(dx, 0); }
}
```

---

## 三, System 持有 Godot Node 类型

### 反例

```csharp
public class CombatSystem
{
    private readonly PlayerNode _player; // 持有具体 Node 类型
    public CombatSystem(PlayerNode player) { _player = player; }
}
```

### 问题

- System 依赖 Godot API, 无法纯单测
- AI 生成 System 时必须懂 Godot API

### 正例

```csharp
public class CombatSystem
{
    private readonly IPlayerView _player;
    public CombatSystem(IPlayerView player) { _player = player; }
}
```

---

## 四, 同场景内滥用 EventBus

### 反例

```csharp
// 同一个场景内, 按钮点击通过 EventBus 通知父容器
EventBus.Instance.Emit("ButtonClicked", buttonId);
```

### 问题

- 同场景内通信走 EventBus, 调用链不可读
- 事件散落, 难以追踪生产者/消费者

### 正例

```csharp
// 同场景内用 Godot Signal
button.Pressed += OnButtonPressed;
```

---

## 五, 父子节点内部用 Signal

### 反例

```csharp
public partial class Player : CharacterBody2D
{
    [Signal] public delegate void HealthChangedEventHandler(int value);
    private HealthComponent _health;
    public void Init() { _health.HealthChanged += OnHealthChanged; }
}
```

### 问题

- 父持有子引用, 走 Signal 是过度抽象
- 调用链绕一圈, 可读性差

### 正例

```csharp
public partial class Player : CharacterBody2D
{
    private HealthComponent _health;
    public void Init() { _health.SetCallback(OnHealthChanged); }
}
```

---

## 六, 跨场景 GetNode

### 反例

```csharp
var ui = GetTree().Root.GetNode<UINode>("Game/UI");
ui.UpdateHealth(50);
```

### 问题

- 跨场景直接引用, 违反"跨场景走 EventBus"

### 正例

```csharp
EventBus.Instance.Emit(new HealthChangedEvent(50));
// UI 场景的监听者订阅此事件
```

---

## 七, 深继承树

### 反例

```csharp
public class Entity : Node2D { }
public class Character : Entity { }
public class Player : Character { }
public class Enemy : Character { }
public class Boss : Enemy { }
```

### 问题

- 继承层级深, 改动父类影响所有子类
- 无法灵活组合能力

### 正例

```csharp
public partial class EntityNode : Node2D
{
    // 组合多个 Component
}
public partial class HealthComponent : Node { }
public partial class MoveComponent : Node { }
public partial class CombatComponent : Node { }
```

---

## 八, Node 直接调 System 内部

### 反例

```csharp
public partial class PlayerNode : CharacterBody2D
{
    private PlayerSystem _system;
    public void OnInput() { _system._internalState++; } // 直接改 System 内部
}
```

### 问题

- 表现层侵入逻辑层内部, 违反边界
- System 状态被 Node 直接修改, 难以追踪

### 正例

```csharp
public partial class PlayerNode : CharacterBody2D, IPlayerView
{
    private PlayerSystem _system;
    public void OnInput() { _system.HandleInput(); } // 调 public API
}
```
