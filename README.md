# vulnhunter-skill

原引擎仓库：https://github.com/Clouditera/VulnHunter-engine

VulnHunter 白盒代码审计的 **zcode 插件**：主会话负责交互确认与调度，subagent 按角色执行；每个 subagent 只挂载自己任务相关的技能。

## 结构

```
.zcode-plugin/plugin.json     插件清单
skills/
  audit-lite/                 主会话调度规则（交互四问、派发循环、coverage.md 台账、纪律红线）
  onboard/ research/ build/ hunt/ verify/   五本任务书（各自独立技能）
  project-profiler/ threat-modeling/ wiki-maintainer/ bug-report/   原引擎技能（随角色挂载）
agents/
  onboard.md research.md build.md hunt.md verify.md   角色 agent 定义（skills/tools/model）
commands/audit.md             /audit 入口命令
```

## 角色与技能挂载

| 角色 | agent 类型 | 挂载技能 |
|---|---|---|
| onboard | `onboard` | onboard + project-profiler + wiki-maintainer |
| research | `research` | research + threat-modeling + wiki-maintainer |
| build | `build` | build + project-profiler |
| hunt | `hunt` | hunt + threat-modeling + wiki-maintainer |
| verify | `verify` | verify + bug-report + wiki-maintainer |

## 安装

### 方式一：插件市场（推荐）

仓库根目录带有 `marketplace.json`，可直接作为 zcode 插件市场源：

1. 打开 zcode「插件市场 → 新增」，填入仓库地址 `https://github.com/Clouditera/Vulnhunter-Skill.git`；
2. 在市场中选择 `vulnhunter-audit` 安装即可。

也可以用命令行添加市场：

```sh
zcode plugins marketplace add https://github.com/Clouditera/Vulnhunter-Skill.git
```

### 方式二：本地链接

```sh
zcode plugins link /path/to/vulnhunter-skill
```

## 使用

安装插件后，在项目里发起：

```sh
zcode --prompt '使用 audit-lite 技能对当前项目做白盒安全审计。审计目标：…；审计范围：…；不做动态验证；漏洞关注类型：全部。开始吧。' \
  --cwd /path/to/project --mode yolo --output-format stream-json --no-color
```

产物写在被测项目的 `.vulnforge/`（knowledge/、hyps.jsonl、findings/、coverage.md、audit_scope.md；动态模式另有 build/env.md）。被测源码只读。

## 验证记录
- minihttpd（52 行 C）：3 HYP，2 confirmed / 1 refuted，约 9 分钟
- json-c 子集（2065 行）：11 HYP，3 confirmed / 7 refuted / 1 证据不足，约 75 分钟；台账与真实 Read 记录对账无虚标
