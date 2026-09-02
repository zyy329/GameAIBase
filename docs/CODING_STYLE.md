# 项目编码风格规范

> 本文件仅规定**项目特有的**编码约定.基准规则参照 `.trae/rules/coding-style.md`

---

## 一,命名增补规则

### 1.1 严格化禁忌

-   布尔变量/属性使用 `is`,`has`,`can`,`should` 前缀,例如:`isVisible`,`hasPermission`,`canMove`,`shouldRespawn`
-   不使用单字母变量(循环变量 `i`,`j`,`k` 除外)
-   不使用缩写(通用缩写如 `ID`,`UI`,`FPS` 除外)

### 1.2 命名后缀规则

-   `*Component`: 可复用组件(`Node` 派生),如 `HealthComponent`
-   `*System`: 逻辑层纯 C# 类(不继承 `Node`),如 `CombatSystem`
-   `*Event`: EventBus 事件(`readonly struct` 或 `record`),如 `PlayerDiedEvent`
-   `*View`: 表现层接口(System 持有),如 `IPlayerView`
-   `*Node`: Godot 节点实现(实现 IView),如 `PlayerNode`
-   `*Data`: 数据 Resource 类(绑定 `.tres` 数据资产),如 `WeaponData`

---

## 二,文件组织

### 2.1 文件结构

-   一个类一个文件,文件名与类名一致

### 2.2 文件内成员排列顺序

类成员按以下顺序排列:

1.  常量 (`const`),枚举 (`enum`) 与静态字段 (`static`)
2.  `[Signal]` 委托声明
3.  `[Export]` 属性
4.  其他属性 (`property`)
5.  实例字段(`private` 字段在前,`public` 字段在后)
6.  构造函数 (`constructor`)
7.  Godot 生命周期方法 (`_Ready`, `_Process`, `_PhysicsProcess`, `_Input`)
8.  公共方法 (`public`)
9.  私有方法 (`private`)
10.  Signal 回调 (`On` 前缀)
11.  嵌套类型 (`nested types`)

---

## 三,Godot 项目特有约定

### 3.1 节点获取与缓存

-   在 `_Ready()` 中获取节点并缓存到私有字段,禁止每帧调用获取节点
-   推荐使用 `%UniqueName` 快捷方式获取节点

---

## 四,代码格式

### 4.1 `using` 排序增补

基础顺序遵循 C# 设计指南,本项目按 CCGS 规则调整分组顺序(按顺序):

1.  Godot 命名空间
2.  系统命名空间(`System.*`)
3.  第三方库
4.  项目命名空间

每组之间空一行

---

## 五,注释规范

### 5.1 TODO 标记

-   使用:`// TODO: 描述`
-   避免使用 `HACK`,`FIXME`,`XXX` 或其他非标准标记
