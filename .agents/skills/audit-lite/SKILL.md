---
name: audit-lite
description: 白盒代码审计（主会话调度版）。用户要求对项目做安全审计、找代码漏洞时使用。主会话负责交互确认与调度，具体执行派 subagent。
---

# audit-lite：主会话调度规则

你是**主会话**：负责与用户交互、确认审计目标、派发 subagent、跟踪全局进展。**不要自己逐行读代码做审计**——那活交给 subagent。

## 第一步：交互确认（必须有）

开始前向用户确认四件事（用户已答全则跳过）：
1. **审计目标**：模糊功能点？具体模块？某个 commit / PR？
2. **审计范围**：哪些代码在范围内（全部 / 某目录 / 某文件清单）
3. **动态验证**：要不要做？用什么环境（本机/ssh 沙箱/容器）
4. **漏洞关注类型**：全部 / 某几类（如注入、越权、SSRF）

确认后把结论写到工作区 `audit_scope.md`：目标原文、范围清单、动态与否、关注类型。**后续目标变化（用户追加说明）时更新它。**

## 第二步：建立工作区（沿用现有约定）

在被测项目根的 `.vulnforge/` 下（**只写这个目录，绝不改被测源码**）：
- `knowledge/wiki/`：项目画像（onboard 产出）
- `knowledge/security.md`：项目安全准则（research 产出；**找漏洞必须符合项目安全说明**，它说不算风险的不报）
- `hyps.jsonl`：风险假设，一行一条 JSON：`{"id":"H1","body":"<file:line + 一句话主张>","tag":"hunt1","state":"new|running|confirmed|refuted","finding_id":null}`
- `findings/<ID>/report.md`：漏洞核心资料（标题/类型/CVSS/位置/数据流/证据/复现；**不写可利用性评分、不做组合利用**）
- `coverage.md`：**审计台账**（见下）
- `build/env.md`：动态环境说明（仅动态模式）

## 第三步：分派与循环

**顺序**：
1. 派 `onboard` subagent（fresh，读 references/onboard.md 的任务书）
2. 派 `research` subagent（fresh，读 references/research.md 的任务书；产出 knowledge/security.md）
3. 动态模式才派 `build` subagent（读 references/build.md；产出 build/env.md）
4. **hunt 循环**：每轮派一个 `hunt` subagent（fresh；读 references/hunt.md 的任务书 + 给它指定本轮的功能点）。它读完会追加 hyps.jsonl 并更新 coverage.md。停轮由**你**判断：
   - **完成**：coverage.md 显示范围内已读完（未读清单为空）
   - **收场**：连续 2-3 轮无新发现且无明显未读区域——在台账里如实标记「未达标收场」，不要硬凑
5. **verify**：对每条 `new/running` 的 HYP 派一个 `verify` subagent（读 references/verify.md 的任务书 + 该条 HYP 原文；可用 fork 继承对应 hunt 会话上下文）。复核三态：confirmed（写 findings/<ID>/report.md 并回写状态）/ refuted / 证据不足保留待查
6. **完成条件**：coverage.md 未读清单为空 **且** hyps.jsonl 里没有 new/running。二者缺一就不能宣称完成

## coverage.md 台账格式（结构化，别写散文）

```
# 审计台账
## 范围
<audit_scope.md 的范围清单>
## 已读
- <file>: <行区间>[, <行区间>...]
## 未读
- <file>: <未读行区间或整文件>
```
hunt 每轮更新「已读/未读」。**只记真实读过的行**，没读不要记。你（主会话）可以抽查核对。

## 纪律红线

- 不提前盖章：目标未达成就继续派单，宁可多一轮
- 证据优先：verify 以代码证据为准，**主动证伪优先**；仅「看起来像」不算数
- 安全准则：research 的 security.md 是找漏洞的口径，不要报它排除的类型
- 工作区隔离：所有产物只写 `.vulnforge/`，被测源码一个字节都不许改
