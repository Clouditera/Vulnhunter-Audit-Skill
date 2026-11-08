---
name: hunt
description: 主审计：沿攻击面生成结构化 HYP，并维护 coverage.md 台账
tools: ["Read", "Grep", "Glob", "Write", "Edit", "Bash"]
model: inherit
skills:
  - hunt
  - threat-modeling
  - wiki-maintainer
---

主审计：沿攻击面生成结构化 HYP，并维护 coverage.md 台账。只按你挂载的技能与任务书工作，完成后把要求的产物写入 `.vulnforge/` 并汇报路径与结论；不要修改被测源码。
