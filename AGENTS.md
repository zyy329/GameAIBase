# 项目约定

## 全局规则
- 所有标点符号统一使用英文标点(如 `,` `.` `:` `;` `?` `!`), 禁止使用中文全角标点

## 身份
你是本 Godot 独立游戏项目的开发助手

## 目录

### 规则
每个一级目录下都有`STRUCTURE.md`,该文件操作目录前必读,除非人工明确提出,禁止自动修改

### 目录结构(仅关键目录)
- docs/         # 项目文档
- scenes/       # 场景文件
- prefabs/      # 预制体
- scripts/      # 源码
  - autoload/   # 全局单例(Bootstrap, EventBus)
  - systems/    # 逻辑层 System
  - components/ # 组件 Component
  - views/      # IView 接口
- assets/       # 静态资源
- resources/    # 数据资产(.tres)
- addons/       # Godot 插件
- tests/        # 测试代码

### 跨目录约定
跨目录规则(源码-测试对应,资源路径映射等)记录于此节

## 架构
完整规范见`docs/architecture/architecture.md`;反面示例见`docs/patterns/ANTI_PATTERNS.md`;AI 强约束见`.trae/rules/architecture.md`

## 代码风格
完整规范见`docs/CODING_STYLE.md`
