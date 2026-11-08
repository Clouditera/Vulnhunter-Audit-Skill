# vulnhunter-skill

原引擎仓库：https://github.com/Clouditera/VulnHunter-engine

白盒代码审计的 zcode skill：主会话负责交互确认与调度，subagent 分工执行 onboard / research / build / hunt / verify。

## 组成
- `.agents/skills/audit-lite/SKILL.md`：主会话调度规则（交互四问、audit_scope、调度循环、coverage.md 台账格式、纪律红线）
- `references/`：五本 subagent 任务书（onboard / research / build / hunt / verify）

## 使用
把 `.agents/skills/audit-lite/` 放进被测项目根（或其上层工作区），用 zcode CLI 以自然语言发起审计，例如：

```sh
zcode --prompt '使用 audit-lite 技能对当前项目做白盒安全审计。审计目标：…；审计范围：…；不做动态验证；漏洞关注类型：全部。开始吧。' \
  --cwd /path/to/project --mode yolo --output-format stream-json --no-color
```

产物写在被测项目的 `.vulnforge/`（knowledge/、hyps.jsonl、findings/、coverage.md、audit_scope.md；动态模式另有 build/env.md）。被测源码只读。

## 验证记录
- minihttpd（52 行 C）：3 HYP，2 confirmed / 1 refuted，约 9 分钟
- json-c 子集（2065 行）：11 HYP，3 confirmed / 7 refuted / 1 证据不足，约 75 分钟；台账与真实 Read 记录对账无虚标
