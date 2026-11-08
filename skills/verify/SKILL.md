---
name: verify
description: HYP 研判：主动证伪或确认，confirmed 产出 findings 报告与 CVSS/CWE
---

# verify 任务书（单条 HYP 复核）

对主会话给的一条 HYP 做独立复核，**主动证伪优先**。

## 做什么
1. 读 HYP 的 file:line 原文与上下文，重走它声称的数据流
2. 逐项核对：触发路径真实可达？危险输入真的可控？关键校验真的缺失或不够？证据（file:line + 代码片段）真实存在且没断章取义？
3. 主动找反证：存在有效校验、路径不可达、仅测试/死代码、仅理论推测——任一成立即倾向 refuted
4. 对照 knowledge/security.md：该风险在项目准则里是否被排除
5. 动态模式：按 build/env.md 用 PoC 复现（成功/失败如实记录；写最小触发脚本）

## 产出（三态回写）
- **confirmed**：在 `.vulnforge/findings/H<id>/report.md` 写漏洞核心资料（标题/类型/CVSS/位置/数据流/证据/复现），并把 hyps.jsonl 该条 state 改为 confirmed、finding_id 填上
- **refuted**：hyps.jsonl 该条 state 改 refuted，附一句证伪理由（写进该条 JSON 的 reason 字段）
- **证据不足**：state 保持 new，写清缺什么，不要强行下结论

## 边界
只改 hyps.jsonl 对应条目的状态与新建 findings/ 报告；不改被测源码、不动其他 HYP。
