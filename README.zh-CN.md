[English](README.md) | 简体中文

# how-to-read-code

[![skills.sh](https://skills.sh/b/lichuang/how-to-read-code)](https://skills.sh/lichuang/how-to-read-code)
![License](https://img.shields.io/badge/license-Apache--2.0-blue)

一个 [Agent Skill](https://agentskills.io)，让你的 AI 编程助手变成一名训练有素的"读码工程师"。

大多数 agent 面对"这个项目是怎么实现的？"这类问题时，只会静态地列文件、凭猜发作答。本 skill 强制执行一套经多年验证的读码方法论——提炼自 codedump 的文章[《如何阅读一份源代码？（2020年版）》](https://www.codedump.info/zh/post/20200605-how-to-read-code/)（[英文版 2021](https://www.codedump.info/en/post/20200605-how-to-read-code/)），沉淀自作者多年源码分析实践（Nginx、Lua、LevelDB、etcd）——让 agent 像资深工程师一样阅读陌生代码：

- **先跑起来** —— 深度阅读前先把项目编译运行起来；尽量精简调试干扰（单进程、`-O0 -g`）。
- **明确目的** —— 阅读前先问一个聚焦问题；后续一切工作以此为准绳。
- **区分主线与支线** —— 主线纵向精读；支线当黑盒，只记录对外接口。
- **横向先于纵向** —— 先整体架构，再追具体执行路径。
- **情景分析** —— 构造真实输入、在入口函数断点、抓取调用栈：跑出来的路径是事实，不是猜测。
- **测试用例即场景文档** —— 把项目自带的测试当作现成的阅读入口。
- **数据结构优先** —— 先梳理核心数据结构及其关系，它们定义了程序架构。
- **没有输出等于没读** —— 每次读码以书面报告收尾（图 > 文 > 代码）。

## 最终交付物

本 skill 的最终产物是一份结构化的**读码报告**——包含：范围与目的、架构图、核心数据结构关系、目标流程的纵向分析、有意未展开的黑盒清单、设计点评与遗留问题。报告结论一律标注 `[verified]`(经运行/调试/测试验证) 或 `[static]`（仅静态推断），哪些是实锤、哪些是猜测，一目了然。

模板见 [`templates/report-template.md`](templates/report-template.md)；从原文到 agent 工作流的转化判据见 [`references/method-notes.md`](references/method-notes.md)。

## 实战样例

[`examples/`](examples/) 目录收录了一份用本 skill 真实产出的报告——分析 Redis（[redis/redis](https://github.com/redis/redis) @ `1f245638`）如何把一条客户端命令从 TCP 字节流处理到写回 socket。`make noopt` 真机编译运行、lldb 实抓调用栈（是 ground truth 而非静态推断）、全部 `file:line` 引用经人工比对锁定 commit 核验。中英双版本齐备，并附触发用的确切提示词：

- 中文：[`examples/redis-client-request.zh.md`](examples/redis-client-request.zh.md)
- English: [`examples/redis-client-request.en.md`](examples/redis-client-request.en.md)

## 安装

```bash
# 全局安装（所有项目可用）
npx skills add lichuang/how-to-read-code -g

# 项目级安装
npx skills add lichuang/how-to-read-code

# 指定 agent，非交互式（适合 CI）
npx skills add lichuang/how-to-read-code -g -a claude-code -a opencode -y
```

支持 [75+ 个 agent](https://github.com/vercel-labs/skills#supported-agents)，包括 Claude Code、OpenCode、Codex、Cursor、Gemini CLI、GitHub Copilot 等。

## 用法

安装后自然提问即可——请求匹配时 agent 会自动加载本 skill：

- *"帮我读一下这个项目"*
- *"分析一下 etcd 的 MVCC 存储是怎么实现的"*
- *"调研一下这个仓库——调度器是怎么调度任务的？"*

也可以显式点名，确保走完整工作流：

- *"用 how-to-read-code 的方式分析 LevelDB 的 compaction"*

**提示**：开头就说清目的（"我只关心读路径""不用搭环境，静态分析就行"）——本 skill 的第一步是锁定范围，你先讲清楚能省一轮来回。

## 示例会话

```
你:      用 how-to-read-code 的方式分析 etcd 的 storage 实现
agent:   [加载 skill]
         Phase 2   先问一个聚焦问题：关心读写路径，还是整体存储架构？
         Phase 1   编译跑起来，切换到利于调试的配置
         Phase 4-7 横向过一遍架构 → 选定纵向路径，
                   用项目自带的测试作为场景入口，
                   从真实运行中抓取调用路径
         Phase 9   写出 `code-reading-report.md`：
                   架构图 + 核心数据结构关系 + 已验证的调用链 +
                   黑盒清单 + 设计点评
你:      （继续追问——报告落盘，跨会话上下文不丢）
```

## 背景

方法论出自 codedump 的文章[《如何阅读一份源代码？》](https://www.codedump.info/zh/post/20200605-how-to-read-code/)（2020 年中文原版，[2021 年英文版](https://www.codedump.info/en/post/20200605-how-to-read-code/)），作者在《Lua 设计与实现》等著作和多篇源码分析文章中反复应用过这套方法。本 skill 将这套面向人的方法论转化为 agent 可执行的 Phase 流程——转化决策记录在 [`references/method-notes.md`](references/method-notes.md)。

## 许可

[Apache-2.0](LICENSE)