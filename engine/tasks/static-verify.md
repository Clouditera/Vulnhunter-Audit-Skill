接下来执行 static-verify 任务。

# 目标

对一条风险假设（HYP）做纯静态的成立性研判：攻击路径是否可达、危害是否成立。成立则产 finding，不成立则把理由写回 HYP（裁决追加）。

# 工作方式

- 读任务正文给定的 HYP（`artifact({action:"get", id:"<HYP id>"})`，任务正文给出 id），按其主张、锚点与边界聚焦。
- HYP 正文中支撑前提的事实断言（格式能力、库行为等）需复核；不成立的，在裁决追加中修正，不得原样带进 finding。
- 沿攻击者视角走一遍：source → 传播 → 校验 → 状态 → sink 能否连成可解释的路径，关键判断落到代码锚点。可行性存疑时，依据代码性质具体推导（匹配算法、数据结构、协议字段的实际性质），让结论落在代码事实上。
- 对照口径：读 `knowledge/security.md`。口径明确把这类问题定性为允许存在的（by-design、文档化缓解、安全策略排除），则 `finding_class` 置 risk。
- 裁决轴：只有确认不可达、攻击者前提不符合威胁模型或找到有效防护时才判 refuted；静态无法确定不构成证伪理由——可疑但证不了的一律 confirmed（不证伪）。可达性依赖目标外组件行为（如浏览器、中间件）的尤其照此处理，不要长篇摇摆。
- 分类轴：`finding_class` 按实际影响定 vulnerability / risk；凡结论依赖环境前提的 confirmed，summary 必须一句话注明前提 + 置信度。
- 评分口径：`artifact({action:"create", kind:"finding", …})` 前先读 bug-report 技能（`skills/bug-report/SKILL.md`）；CVSS 与 EV 一律用 `python3 skills/bug-report/cvss-calc.py` 计算，禁止手算，向量与分数以脚本输出为准。
- 成立 → 用 `artifact({action:"create", kind:"finding", fields:{…}})` 落 finding，并用 `artifact({action:"update", id:"<HYP id>", state:"confirmed", fields:{conclusion:"confirmed", verdict:"…"}})` 把研判追加进 HYP。
- 不成立 → 用 `artifact({action:"update", id:"<HYP id>", state:"refuted", fields:{conclusion:"refuted", verdict:"…"}})` 写回研判理由，如实收尾。

# 产出要求

- **finding**（artifact create，kind=finding）：结论准确可复现——标题一句话说清漏洞，summary 一段读懂，dataflow 逐步落到代码锚点，附 CVSS 定级与修复建议（明确时给最小 diff）。fields 至少含：title、summary、vuln_type、finding_class、cwe、cvss_vector、cvss_score、affected_versions、dataflow、reproduction、fix_patch、references。
- **HYP 裁决回写**（artifact update）：conclusion + verdict（含口径核验），供后续追溯。

# 备注

- 不编译、不运行目标代码，不连接沙箱；纯静态证据。
- 锚点核实：finding 里的 `file:line` 必须经 `grep -n` 核实。
