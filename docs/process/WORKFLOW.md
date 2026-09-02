# 开发流程

> 改编自 CCGS 七阶段管线, 已映射到本项目目录. 模板在 docs/templates/, 阶段门禁为自检清单, 不强制阻塞

## 阶段与产出物

| # | 阶段 | 关键产出物 | 模板 |
|---|------|-----------|------|
| 1 | Concept 概念 | docs/design/game-concept.md, docs/design/systems-index.md | game-concept, systems-index |
| 2 | Systems Design 系统设计 | docs/design/gdd/ 每系统一份 GDD(8 节必备) | game-design-document |
| 3 | Technical Setup 技术设定 | docs/architecture/architecture.md, adr-*.md(至少 3 份), control-manifest.md | architecture-decision-record, architecture-traceability |
| 4 | Pre-Production 预生产 | docs/design/ux/ 关键界面(主菜单, HUD, 暂停), epics/stories, 首个冲刺计划 | ux-spec, sprint-plan |
| 5 | Production 生产 | 按 sprint 实现 story: 就绪检查 → 实现 → 代码评审 → 验收关闭 | milestone-definition, test-plan |
| 6 | Polish 打磨 | 性能剖析, 平衡检查, 至少 3 份试玩报告 | test-evidence |
| 7 | Release 发布 | 发布清单, 补丁说明, 更新日志 | release-checklist-template, changelog-template |

规划与追踪产物统一放 docs/production/(epics/, sprints/, milestones/, playtests/)

## 使用方式

- 进入新阶段前, 对照上表自检产出物齐备
- 设计文档先骨架后逐节填充, 每节经人工确认再写入(见 .trae/rules/design-docs.md)
- story 生命周期: 就绪检查 → 实现 → 代码评审 → 验收关闭; bug 修复必附回归测试
- 冲刺结束做回顾(post-mortem), 再规划下一冲刺
- AI 建议 Godot API 前先查 docs/engine-reference/godot/(版本校验记录与废弃 API)
- 改 GDD 后检查受影响的 ADR 与 story(模板: architecture-traceability)
