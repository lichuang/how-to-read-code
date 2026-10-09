# how-to-read-code

English | [简体中文](README.zh-CN.md)

[![skills.sh](https://skills.sh/b/lichuang/how-to-read-code)](https://skills.sh/lichuang/how-to-read-code)
![License](https://img.shields.io/badge/license-Apache--2.0-blue)

An [Agent Skill](https://agentskills.io) that turns your coding agent into a disciplined code reader.

Most agents answer "how does this project work?" by statically listing files and guessing at the architecture. This skill imposes a proven reading methodology — distilled from codedump's essay [*How to Read Code* (2021)](https://www.codedump.info/en/post/20200605-how-to-read-code/) ([Chinese original, 2020](https://www.codedump.info/zh/post/20200605-how-to-read-code/)) and refined across years of source-analysis blogging (Nginx, Lua, LevelDB, etcd) — so the agent works like a senior engineer reading unfamiliar code:

- **Run it first** — get the project compiling and executing before deep reading; minimize debug noise (single-process, `-O0 -g`).
- **Clarify the purpose** — one focused question before reading; scope everything against it.
- **Mainline vs subplot** — read the mainline vertically; treat subplots as black boxes with documented interfaces.
- **Horizontal before vertical** — architecture first, then follow specific execution paths.
- **Scenario analysis** — construct real inputs, break at entry points, capture backtraces: observed paths are ground truth, not speculation.
- **Tests as scenario documents** — use the project's own test cases as ready-made reading entry points.
- **Data structures first** — map the core structures and their relationships; they define the architecture.
- **Output or it didn't happen** — every reading session ends with a written report (diagrams over prose over code).

## The deliverable

The skill's final artifact is a structured **code reading report** — scope, architecture diagram, core data-structure relationships, vertical analysis of the target flow, consciously-unopened black boxes, design commentary, and open questions. Report conclusions are labeled `[verified]` (observed via execution) or `[static]` (inferred from reading only), so you always know what is proven versus guessed.

A template ships in [`templates/report-template.md`](templates/report-template.md); the article-to-workflow conversion rationale lives in [`references/method-notes.md`](references/method-notes.md).

## Worked example

See [`examples/`](examples/) for a real report produced with this skill — an analysis of how Redis ([redis/redis](https://github.com/redis/redis) @ `1f245638`) processes a client request command from TCP bytes to reply socket. The server was actually built and run, call stacks were captured with lldb (ground truth, not static inference), and every `file:line` reference was manually verified against the pinned commit. Both Chinese and English versions are included, along with the exact prompt that triggered it:

- Chinese: [`examples/redis-client-request.zh.md`](examples/redis-client-request.zh.md)
- English: [`examples/redis-client-request.en.md`](examples/redis-client-request.en.md)

## Install

```bash
# Global (available in all projects)
npx skills add lichuang/how-to-read-code -g

# Project-scoped
npx skills add lichuang/how-to-read-code

# Target specific agents, non-interactive (CI friendly)
npx skills add lichuang/how-to-read-code -g -a claude-code -a opencode -y
```

Works with [75+ agents](https://github.com/vercel-labs/skills#supported-agents) including Claude Code, OpenCode, Codex, Cursor, Gemini CLI, and GitHub Copilot.

## Usage

Once installed, just ask naturally — the agent loads the skill when a request matches:

- *"Analyze how etcd's MVCC storage works"*
- *"Help me read through this project"*
- *"Investigate this repo — how does the scheduler schedule tasks?"*

Or invoke it explicitly to guarantee the full workflow:

- *"Analyze LevelDB's compaction using the how-to-read-code workflow"*

**Tip**: state your purpose up front ("I only care about the read path", "no need to build it, static analysis is fine") — the skill's first move is to pin down scope, so saying it yourself saves a round trip.

## Example session

```
you:    Analyze etcd's storage implementation using the how-to-read-code workflow
agent:  [loads skill]
        Phase 2  asks one focused question: read/write path, or overall storage architecture?
        Phase 1  builds the project, runs it, switches to a debug-friendly config
        Phase 4-7  horizontal architecture pass → picks the vertical path,
                   uses the project's own tests as scenario entry points,
                   captures the call path from a real run
        Phase 9  writes `code-reading-report.md`:
                 architecture diagram + core data-structure map +
                 verified call chain + black boxes + design commentary
you:    (follow-up questions — the report persists, context survives across sessions)
```

## Background

The methodology comes from codedump's essay [*How to Read Code*](https://www.codedump.info/en/post/20200605-how-to-read-code/) (2021, [Chinese original 2020](https://www.codedump.info/zh/post/20200605-how-to-read-code/)), itself applied in the author's books and many source-analysis articles, including *Lua Design and Implementation*. The skill converts that human-learner methodology into agent-executable phases — the conversion decisions are documented in [`references/method-notes.md`](references/method-notes.md).

## License

[Apache-2.0](LICENSE)