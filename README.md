# vulnhunter-skill

原引擎仓库：https://github.com/Clouditera/VulnHunter-engine

VulnHunter 白盒代码审计的 **zcode 插件**：主会话负责交互确认与调度，subagent 按角色执行；每个 subagent 只挂载自己任务相关的技能。

## 场景介绍

唯一对外主场景是**白盒代码安全审计**（`/audit` 命令或自然语言触发）：主会话（audit-lite 技能）负责交互确认与调度，不做逐行审计，具体执行派发给五个角色 subagent。

**输入**（交互四问，确认后写入 `.vulnforge/audit_scope.md`）：

| 输入项 | 说明 |
|---|---|
| 审计目标 | 模糊功能点 / 具体模块 / 某个 commit 或 PR |
| 审计范围 | 全部代码 / 某目录 / 某文件清单 |
| 动态验证意愿 | 是否做动态验证；运行环境（本机 / SSH 沙箱 / 容器） |
| 漏洞关注类型 | 全部 / 指定几类（注入、越权、SSRF 等） |

**输出**（全部落在被测项目 `.vulnforge/`，被测源码只读）：`knowledge/wiki/` 项目画像、`knowledge/security.md` 安全准则与威胁模型、`hyps.jsonl` 风险假设台账、`coverage.md` 覆盖台账、`findings/<ID>/report.md` 漏洞报告（CVSS/CWE/利用价值/复现/补丁）；动态模式另有 `build/env.md`。

**五个执行子场景**（按派发顺序）：

| 角色 | 干什么 | 输入 → 输出 |
|---|---|---|
| onboard | 项目画像与入口发现 | 项目根 → `knowledge/wiki/overview.md`、`index.md` |
| research | 技术研究与威胁建模 | 画像 + README/docs/git log → `knowledge/security.md`（含排除条款、风险面清单） |
| build | 动态环境构建（仅动态模式） | 构建文件 + 指定环境 → `build/env.md` |
| hunt | 主审计（多轮循环，每轮一个功能点） | coverage 未读清单 + 安全准则 → 追加 `hyps.jsonl`、更新 `coverage.md` |
| verify | 逐条 HYP 研判，主动证伪优先 | 单条 HYP → 三态回写；confirmed 产出 `findings/H<id>/report.md` |

**主要工具**：

- 通用：Read / Grep / Glob / Write / Edit / Bash（所有 subagent；产物只写 `.vulnforge/`）
- 编排：Agent 派发工具（主会话 → 各角色 fresh 会话）
- 专用脚本：`project-profiler/profile-basic-scan.py`（项目画像扫描）、`bug-report/cvss-calc.py`（CVSS v3.1 / 利用价值 EV 评分）

**完成双门槛**：`coverage.md` 未读清单为空 **且** `hyps.jsonl` 无 new/running；verify 以代码证据为准、主动证伪优先；只报 `security.md` 准则内的风险类型。

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
