---
name: research
description: 技术研究与威胁建模：基于项目画像产出技术档案、威胁模型与 security.md 排除条款
---

# research 任务书

调研项目弱点与项目安全说明，形成找漏洞的口径。

## 做什么
1. 读 README、docs/、CHANGELOG、git log（如可用）里的安全相关信息：历史漏洞修复、已知风险、攻击面说明
2. 写出**项目安全准则**：什么算这个项目的风险（鉴权绕过、注入、越权、SSRF、路径穿越、DoS 等）以及**项目明确不算风险的**（如「不接受 DoS 类报告」「不覆盖 X 模块」）
3. 结合项目画像（knowledge/wiki/overview.md）标出重点风险面：外部输入入口（HTTP 参数、文件上传、协议解析）、危险 Sink（命令执行、SQL、文件读写、反序列化）

## 产出
- `.vulnforge/knowledge/security.md`：项目安全准则（找漏洞要符合它；排除项明确写出）
- 重点风险面清单（入口 → Sink 的方向提示，供 hunt 用）

## 边界
只做本地调研与代码结构分析，不联网检索（除非主会话明确允许）。产物只写 .vulnforge/。
