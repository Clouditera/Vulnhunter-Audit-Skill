接下来执行 research 任务。

# 目标

通过代码文件之外的内容，找出对当前漏洞审计有价值的信息：在线资料（CVE/GHSA/issue/writeup）与 git 信息（版本、安全修复史、回退记录、变更热点）。产出情报档案，更新安全基线（security.md）与 wiki；情报指向的风险线索可直接落成风险假设（HYP）。

# 工作方式

- 先读 `knowledge/research/` 已有档案、`knowledge/security.md` 与项目画像，确认项目与版本，增量推进。
- 在线检索：历史 CVE/GHSA、上游 issue/commit 安全动态、官方安全公告、同类项目缺陷模式、部署配置风险共识，写情报笔记（write/edit）。可用工具：`web_search`、`fetch_content`、`get_search_content`、`source_check`。
- git 考古：跑 git log/tag 查版本演进、安全修复史、回退记录与变更热点，补在线检索之外的线索。
- **安全基线 online 增量**：补官方安全策略与漏洞提交政策、历史漏洞的维护者处置态度（认可修复还是 won't fix）、issue/PR 表态，写进 `knowledge/security.md`；威胁模型有修正也在此更新。
- **wiki 更新**：值得沉淀的项目背景与攻击面认知写入 `knowledge/wiki/`（write/edit）。
- **风险假设**：情报明确指向某处代码风险时，可用 `artifact({action:"create", kind:"hyp", fields:{body:"…"}})` 直接落成 HYP（body 带 file:line 锚点与一句话主张；源码只做存在性抽查，精读交给 hunt）。低置信度但具体可查的也落。
- 在线与 git 通道都缺失、且对项目没有明确记忆时，如实收尾；零产出是合法结果，情报只写有出处的。

# 产出要求

- **knowledge/research/**：情报档案——index.md 维护主题目录，每个主题一个文档（历史漏洞与修复、上游安全动态、官方安全公告、同类项目缺陷模式、部署配置风险共识等），每条情报注明来源 URL。
- **knowledge/security.md**：online 增量——官方安全策略与漏洞提交政策、历史 CVE/GHSA 维护者处置态度、issue/PR 表态，每条结论标注来源 URL。
- **wiki 增量**：值得沉淀的背景与攻击面认知。
- **风险假设**（artifact create，kind=hyp）：情报指向明确、可检查的代码风险（可选产出）。

# 备注

- 情报只写有出处的；推测与推断明确标注为推测。
- 锚点核实：写入任何产物的 `file:line` 必须经 `grep -n` 核实。
