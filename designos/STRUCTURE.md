# designos/ 目录说明

## 用途
GameDesignOS 项目 workspace, 存放游戏设计决策、假设、证据、实验、设计资产、工作流、学习记录与导出物

## 来源
- Skill 包: `.trae/skills/` (5 个: concept-architect, experience-analyzer, ED-optimizer, proposal-writer, paranoia-evolver)
- Python runtime: `gamedesignos` 包 (已 pip install, 需 `GAMEDESIGNOS_HOME` 环境变量指向源码)
- 契约 schema: `contracts/` (19 个 JSON schema + router.yaml)

## 核心子目录 (9 个生命周期)
- `00-inbox/`: 待分类输入
- `01-decisions/`: 设计决策 (DEC-*.json)
- `02-assumptions/`: 假设登记 (ASM-*.json)
- `03-evidence/`: 证据 (EVD-*.json)
- `04-experiments/`: 实验 (EXP-*/)
- `05-design-assets/`: 设计资产 (GDD, pitch, 概念案等)
- `06-workflows/`: 工作流状态
- `07-learning/`: 复盘学习记录
- `08-exports/`: 导出物 (决策图, 可评审 pack)
- `contracts/`: skill 交接契约 schema (decision, assumption, evidence, experiment, gate, workflow-run 等)
- `.gamedesignos/`: runtime 状态 (gate-results/, workflow-runs/)

## 使用方式
- 自然语言入口: `python -m gamedesignos ask "<你的需求>"`
- 创建项目 workspace: `python -m gamedesignos start "<项目名>"`
- Trae 会话中: skill 按需自动加载, 直接说需求即可

## 规则
- 真实项目资料仅存于此, 不提交到 GameDesignOS 公开仓库
- Human Gate 前停住, AI 不替人接受承诺决策
- 一次实验结果不直接升级为长期规则, 先保持 candidate
