# Examples

English | [简体中文](#简体中文)

---

## English

Worked examples produced with this skill. They demonstrate the full workflow — run it, clarify purpose, horizontal architecture, vertical tracing with scenario analysis, core data structures, black boxes, design commentary — and the resulting report format.

### Redis: the client request handling pipeline

- [`redis-client-request.zh.md`](redis-client-request.zh.md) — Chinese
- [`redis-client-request.en.md`](redis-client-request.en.md) — English

**What it covers**: [redis/redis](https://github.com/redis/redis) `unstable` @ `1f245638` (2023, v7.1-era snapshot). How one command (`SET foo bar`) travels from TCP bytes to the reply socket: `readQueryFromClient` → `processInputBuffer` → `processCommand` (~15 validation steps) → `call` → `addReply` → `beforeSleep` write-back, plus the three data structures that carry it all (event table, connection abstraction, client state).

**Prompt used** (that's all it takes):

```text
Analyze the client request handling pipeline using the how-to-read-code workflow
```

**Why this example is trustworthy**: executed for real — the server was built with `make noopt`, driven with redis-cli, and call stacks were captured with lldb (section 4.1 is the actual captured stack, i.e. ground truth rather than static inference). The [verified]/[static] labels and all `file:line` references were manually checked against the pinned commit (one line-number error was caught and corrected during review — see the appendix).

---

## 简体中文

由本 skill 实际产出的读码报告样例，完整演示了工作流——跑起来、定目的、横向架构、纵向追踪（情景分析）、核心数据结构、黑盒清单、设计点评——以及最终的报告格式。

### Redis：客户端请求处理流程

- [`redis-client-request.zh.md`](redis-client-request.zh.md) — 中文
- [`redis-client-request.en.md`](redis-client-request.en.md) — English

**内容**：[redis/redis](https://github.com/redis/redis) `unstable` 分支 @ `1f245638`（2023 年 / v7.1 时代快照）。一条命令（`SET foo bar`）如何从 TCP 字节流走到写回 socket：`readQueryFromClient` → `processInputBuffer` → `processCommand`（约 15 步校验）→ `call` → `addReply` → `beforeSleep` 写回，以及承载整条流水线的三个数据结构（事件表、连接抽象、客户端状态）。

**触发提示词**（就这么一句）：

```text
用 how-to-read-code 的方式分析 处理客户端请求的流程
```

**为什么这个样例可信**：真机执行——`make noopt` 编译、redis-cli 实测驱动、lldb 抓取调用栈（4.1 节就是实际抓到的栈，是 ground truth 而非静态推断）。`[verified]`/`[static]` 标注与全部 `file:line` 引用经人工比对锁定 commit 核验（审阅中发现并修正了一处行号错误，见附录）。