# GameAIBase

个人为开发 Godot 独立游戏搭建的一组 AI 工作流, 方便后续其他项目共用该工作流

## 特性

- 以 AI 为中心的游戏开发工作流 (概念 → 设计案 → 体验浓度优化 → 规则演化)
- 内置技能: `game-concept-architect`, `game-design-proposal-writer`, `game-experience-density-optimizer`, `paranoia-ai-system-evolver`
- 需求管道: 创意轨道 (`/flow-idea`) 与 任务轨道 (`/flow-task`), 支持 `.flow` 会话恢复
- 事件驱动自动化: 基于 EventBus/Bootstrap 的全局单例架构

## 技术栈

- 引擎: Godot 4.x
- 语言: C# (.NET)
- 文档: Markdown + 分层约定 (见 [AGENTS.md](AGENTS.md))

## 快速开始

1. 安装 Godot 4.x 与 .NET SDK
2. 用 Godot 打开本项目根目录
3. 查看 [docs/process/WORKFLOW.md](docs/process/WORKFLOW.md) 了解开发流程

## 游戏内容定位

本仓库既托管 AI 工作流, 也承载具体游戏项目 的内容定位. 游戏方向说明 (玩法定位, 核心循环, 目标) 详见 [docs/design/game-concept.md](docs/design/game-concept.md).

## 文档导航

- [AGENTS.md](AGENTS.md) - 工程约定与目录结构
- [docs/process/WORKFLOW.md](docs/process/WORKFLOW.md) - 开发流程 (七阶段管线)
- [docs/architecture/architecture.md](docs/architecture/architecture.md) - 架构规范
- [docs/CODING_STYLE.md](docs/CODING_STYLE.md) - 代码风格
- [LICENSE](LICENSE)