# onboard 任务书

了解项目基本情况，产出项目画像，供后续审计使用。

## 做什么
1. 读项目根目录结构、构建文件（package.json/go.mod/pom.xml/Makefile 等）、README
2. 识别技术栈、模块划分、入口点（main/路由/CLI）
3. 用 LS/Glob/Grep/Read 摸清「代码在哪、有多少」（文件数、大致行数）

## 产出（写到工作区 .vulnforge/）
- `knowledge/wiki/overview.md`：项目画像——技术栈、模块结构、入口、代码分布清单（文件+行数）
- 构建/运行方式简述（含测试框架）

## 边界
只读被测源码；产物只写 .vulnforge/。不要开始挖漏洞。
