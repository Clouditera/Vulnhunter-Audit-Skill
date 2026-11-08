接下来执行 dynamic-verify 任务。

# 目标

对一条风险假设（HYP）做成立性研判，并对裁决 confirmed 的 vulnerability 立即在沙箱中复现：先用代码路径判断假设是否成立，再用动态证据检验它。

# 工作方式

## 成立性研判

- 读任务正文给定的 HYP（`artifact({action:"get", id:"<HYP id>"})`），按其主张、锚点与边界聚焦。
- HYP 正文中支撑前提的事实断言（格式能力、库行为等）需复核；不成立的，在裁决追加中修正，不得原样带进 finding。
- 沿攻击者视角走一遍：source → 传播 → 校验 → 状态 → sink 能否连成可解释的路径，关键判断落到代码锚点。可行性存疑时，依据代码性质具体推导（匹配算法、数据结构、协议字段的实际性质），让结论落在代码事实上。
- 对照口径：读 `knowledge/security.md`。口径明确把这类问题定性为允许存在的（by-design、文档化缓解、安全策略排除），则 `finding_class` 置 risk。
- 裁决轴：只有确认不可达、攻击者前提不符合威胁模型或找到有效防护时才判 refuted；静态无法确定不构成证伪理由——可疑但证不了的一律 confirmed（不证伪）。可达性依赖目标外组件行为（如浏览器、中间件）的尤其照此处理，不要长篇摇摆。
- 分类轴：`finding_class` 按实际影响定 vulnerability / risk；凡结论依赖环境前提的 confirmed，summary 必须一句话注明前提 + 置信度。
- 评分口径：`artifact({action:"create", kind:"finding", …})` 前先读 bug-report 技能（`skills/bug-report/SKILL.md`）；CVSS 与 EV 一律用 `python3 skills/bug-report/cvss-calc.py` 计算，禁止手算，向量与分数以脚本输出为准。
- 成立 → 用 `artifact({action:"create", kind:"finding", fields:{…}})` 落 finding，并用 `artifact({action:"update", id:"<HYP id>", state:"confirmed", fields:{conclusion:"confirmed", verdict:"…"}})` 把研判追加进 HYP。
- 不成立 → 用 `artifact({action:"update", id:"<HYP id>", state:"refuted", fields:{conclusion:"refuted", verdict:"…"}})` 写回研判理由，如实收尾。

## 动态复现

- 裁决 confirmed 且 `finding_class: vulnerability` 时，转入沙箱复现；`risk` 与 refuted 不做动态复现。
- 动态操作前先确认沙箱可达（按沙箱连接配置短重试不超过 3 次）；不可达时用 `artifact({action:"update", id:"<沙箱状态产物 id>", state:"unavailable", fields:{note:"…"}})` 回报，本任务如实收尾。
- 读 finding 声称的入口、路径与危害，在沙箱内构建目标、编写并运行触发脚本；脚本、触发输入、日志先落本地，再用 `artifact({action:"update", id:"<finding id>", fields:{attachments:[…]}})` 收进附件（attachments 是数组，更新时提交完整列表；元素形如 `{kind:"poc", path:"…", note:"…"}`；沙箱产物先读出并落到本地工作区再登记）。
- 复现成功用 `artifact({action:"update", id:"<finding id>", fields:{poc_status:"reproduced", poc_evidence:"…"}})` 回写；失败/受阻如实回写（fail / blocked）并在 poc_evidence 说明。

# 产出要求

- **HYP 裁决回写**（artifact update）：conclusion + verdict。
- **finding**（artifact create/update）：字段同 static-verify；动态证据（poc_status、poc_evidence、attachments）如实。
- **knowledge/env/**：本次动态操作的关键记录（可选）。

# 备注

- 沙箱是被测目标的唯一动态执行环境；本地只做静态动作。长驻服务/长命令的命令卫生：沙箱内启动 daemon 必须彻底脱离 stdio（`setsid nohup <cmd> >/tmp/x.log 2>&1 </dev/null &`），可能阻塞的命令一律加 `timeout <秒>` 包裹。
- 锚点核实：finding 里的 `file:line` 必须经 `grep -n` 核实。
