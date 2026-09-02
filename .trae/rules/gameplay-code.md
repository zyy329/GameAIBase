---
alwaysApply: false
description: 玩法代码强约束(数值外置, 帧率无关, 状态机显式转换);仅在生成,修改 scripts/ 下代码时启用;普通知识问答不启用
---

# 玩法代码强约束

## 1. 数值外置
- 玩法数值必须来自 Resource(`.tres`)或配置, 禁止硬编码魔法数字
- 调参不改代码

## 2. 帧率无关
- 一切随时间变化的计算必须乘 `delta`

## 3. 职责边界
- 玩法逻辑禁止直接引用 UI, 跨系统通知走 EventBus/Signal(见 architecture.md)

## 4. 状态机
- 引入状态机必须显式转换表, 禁止散落布尔标记拼状态

## 5. 全局状态
- 禁止为玩法状态新建静态单例, 依赖走构造函数注入
- 全局单例仅限 autoload(Bootstrap, EventBus)
