---
name: vulnhunter
description: VulnHunter 白盒代码安全审计套件。当用户要求对项目做安全审计、白盒漏洞挖掘、威胁建模或撰写漏洞报告时使用。内含 10 个按审计流程组织的子技能（画像→研究→审计→研判→报告），按需加载。
---

# VulnHunter 白盒审计套件

对目标代码库做白盒安全审计的完整工具箱，按审计流程分为 10 个子技能，各自独立目录、独立 `SKILL.md`。

## 使用方式

按当前任务阶段，直接阅读对应子技能的 `SKILL.md` 并遵循其指令；子技能内部会再按需引用自己的参考文档、脚本与模板。

## 审计主流程（建议顺序）

1. **onboard** — 项目画像与入口发现：`skills/onboard/SKILL.md`
2. **research** — 技术研究与威胁建模：`skills/research/SKILL.md`
3. **hunt** — 主审计，沿攻击面生成漏洞假设（HYP）：`skills/hunt/SKILL.md`
4. **verify** — 逐条证伪或确认 HYP：`skills/verify/SKILL.md`
5. **bug-report** — 已确认漏洞的结构化报告（CVSS/CWE/利用价值）：`skills/bug-report/SKILL.md`

可选动态验证：需要拉起靶子复现 PoC 时用 **build**（`skills/build/SKILL.md`）。

## 支撑技能（被主流程调用）

- **audit-lite** — 审计调度纪律（交互确认、派发、coverage 台账）：`skills/audit-lite/SKILL.md`
- **project-profiler** — 项目客观事实收集工具（Python 扫描器）：`skills/project-profiler/SKILL.md`
- **threat-modeling** — 威胁建模判断基准：`skills/threat-modeling/SKILL.md`
- **wiki-maintainer** — 审计知识沉淀为项目 wiki：`skills/wiki-maintainer/SKILL.md`

## 纪律

- 被测源码只读，产物写入被测项目的 `.vulnforge/` 目录
- 每个子技能的 `SKILL.md` 是该阶段的唯一权威指令，以其为准
