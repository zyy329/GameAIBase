# 架构设计

> 本文件规定项目架构约定. 目标: vibecoding 友好(AI 可独立生成模块, 人类评审)

---

## 一, 核心原则

5 项核心:

1. **三层架构**(表现/逻辑/数据)
2. **通信分层**: EventBus 仅跨场景; 同场景跨节点用 Godot Signal; 父子节点内部用直接方法
3. **组件化**: 组合优于继承
4. **Pure DI**: 手动构造函数注入, 无 DI 框架
5. **强约束 IView**: System 持有接口, 不持有 Godot Node 类型

---

## 二, 三层架构

```
[表现层] Node/Scene (Godot 节点)
   ↑ IView 接口实现
[逻辑层] System (纯 C# 类, 不继承 Node)
   ↓ 构造函数注入依赖
[数据层] Model/Resource (POCO 或 .tres)
```

### 表现层 (Presentation)
- Godot 节点派生类 (`Node`, `Node2D`, `Control` 等)
- 实现 IView 接口
- 只管显示与输入转发, 不写业务逻辑
- 通过 Signal 或 EventBus 通知逻辑层

### 逻辑层 (Logic)
- 纯 C# 类, 不继承 `Node`, 可单测
- 持有 IView 接口引用, 不持有 Godot Node 类型
- 通过构造函数接收依赖
- 跨场景通信走 EventBus, 同场景内调本地方法

### 数据层 (Data)
- POCO 模型或 Godot `Resource` (`.tres`)
- 不含逻辑, 只含数据

---

## 三, 通信机制

| 场景 | 机制 | 示例 |
|------|------|------|
| 跨场景/跨系统 | EventBus | 战斗系统通知 UI 系统更新血条 |
| 同场景内跨节点松耦合 | Godot Signal | 按钮点击通知父容器 |
| 父子节点内部协作 | 直接方法 | 父节点调用子节点方法 |

### EventBus

- 单一 Autoload `EventBus`
- 只做 `Action` 转发, 同帧派发
- 事件类型用 `readonly struct` 或 `record`, 不可变, 后缀 `Event`

### Signal

- 同场景内跨节点用 Godot 原生 `[Signal]`
- 不滥用, 仅用于"松耦合通知"

### 父子内部方法

- 父持有子引用, 直接调用方法
- 豁免 Signal 约束, 避免过度抽象

---

## 四, Pure DI

- 无 DI 框架, 手动构造函数注入
- 组装根: `scripts/autoload/Bootstrap.cs`
- 在 `_Ready()` 里按依赖顺序 `new` 所有 System, 互相注入

### Bootstrap 示例

```csharp
public partial class Bootstrap : Node
{
    public override void _Ready()
    {
        var clock = new GameClock();
        var repo = new SaveRepository();
        var combat = new CombatSystem(clock);
        var save = new SaveSystem(repo, combat);
        AddChild(combat);
    }
}
```

---

## 五, IView 强约束

- System 持有 IView 接口引用, 不持有任何 Godot 类型
- Node 实现对应 IView 接口
- System 测试时可用 mock IView

### 示例

```csharp
public interface IPlayerView
{
    void PlayMoveAnimation();
    void SetPosition(float x, float y);
}

public partial class PlayerNode : CharacterBody2D, IPlayerView
{
    public void PlayMoveAnimation() { /* 调 AnimationPlayer */ }
    public void SetPosition(float x, float y) { Position = new(x, y); }
}

public class PlayerSystem
{
    private readonly IPlayerView _view;
    public PlayerSystem(IPlayerView view) { _view = view; }
    public void Move(float dx, float dy) { _view.SetPosition(dx * 100, dy * 100); }
}
```

---

## 六, 组件化

- 实体 = Node + 多个 Component (Node)
- Component 自带数据 + 行为
- Component 间不直接引用, 通过 Signal 或 EventBus 协作
- 避免深继承 (如 `Player : Character : Entity`)

### 示例

```csharp
public partial class HealthComponent : Node
{
    [Signal] public delegate void DiedEventHandler();
    public int Max = 100;
    public int Current;
    public void Damage(int n) { /* ... */ }
}
```

---

## 七, 边界规则 (强约束)

1. System 不继承 `Node`
2. System 不主动 `GetNode`
3. System 持有 IView, 不持有 Godot Node 类型
4. Node 不直接调用 System 内部方法 (通过 IView 反向通知或 Signal/EventBus)
5. 父子节点内部方法调用豁免 Signal 约束
6. 跨场景通信必须走 EventBus, 不能 `GetNode` 跨场景引用

---

## 八, 不含项

- **状态机**: 按需引入. 简单敌人用 AnimationTree 内置 FSM; 仅复杂 Boss/玩家引入 `StateMachine<T>`
- **Services**: 已删除, 由 Pure DI 覆盖

---

## 九, 反面示例

见 [docs/patterns/ANTI_PATTERNS.md](file:///docs/patterns/ANTI_PATTERNS.md)
