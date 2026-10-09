# Method Notes: from article to agent workflow

Source: codedump "How to Read Code" (English version, 2021-02-15; original Chinese essay《如何阅读一份源代码？（2020年版）》published 2020-06-05)
- Blog (en, canonical for this skill): https://www.codedump.info/en/post/20200605-how-to-read-code/
- Blog (zh, original): https://www.codedump.info/post/20200605-how-to-read-code/
- Blog (zh, 2019, earlier version): https://www.codedump.info/post/20190324-how-to-read-code/

This file records, per article point, WHY the workflow step exists, the original's key examples, and what was changed when converting from human-learner advice to agent-executable workflow. Consult when you need to justify a step, or when deciding whether a step can be skipped.

## Mapping table

| Article point | Skill phase | Key original example | Conversion note |
|---|---|---|---|
| 先跑起来 (run it) | Phase 1 | Nginx: set workers=1 for deterministic debugging; modify Makefile to `-O0 -g` | Human advice about "building debugging skill" → agent rule: default to debug/minimal builds and single-process configs |
| 明确自己的目的 (clarify purpose) | Phase 2 | Author's Nginx goals: (a) core flow & data structures, (b) how to implement a module | Human advice "don't read without purpose" → agent rule: ask user one focused question, record scope in report |
| 区分主线和支线剧情 (mainline vs subplot) | Phase 3 | "How the dict is implemented" is subplot when reading business flow | Unchanged in spirit; agent version = explicit black-box interface notes in report |
| (pimpl aside) | Phase 3 | C++ header-only-interface / impl class pattern | Kept out of main flow; mention only when relevant |
| 纵向与横向 (vertical & horizontal) | Phase 4 | Vertical = follow a process; horizontal = cross-module architecture | Whole-before-parts advice becomes hard alternation rule with diagram updates |
| 情景分析 (scenario analysis) | Phase 5 | Lua: breakpoint at `luaK_code`, lldb backtrace shows full parse→opcode pipeline; book origin:《Linux内核源代码情景分析》《Windows内核情景分析》 | Human "add breakpoints and observe" → agent rule: construct repro inputs, capture backtrace/variables; debugger optional |
| 利用好的测试用例 (good test cases) | Phase 6 | etcd & Google OSS ship careful tests | Unchanged: tests = pre-built scenarios, entry points for Phase 5 |
| 理清核心数据结构 (core data structures) | Phase 7 | House analogy: structures are the frame, algorithms are rooms; Linus quote; author's leveldb/etcd analysis blogs | Kept; alternation with scenario analysis made explicit (no strict order between the two) |
| 多问自己问题 (ask yourself questions) | Phase 8 | Sample questions: why this data structure? how do similar projects do it? would I design it this way? | Human "learn via input→output" → agent rule: record questions & tradeoff commentary in report |
| 写读码笔记 (write notes) | Phase 9 | Imagine reader = a stranger or yourself months later; avoid large code pastes (pseudo-code instead); fork + annotate in own GitHub (etcd-3.1.10-codedump); "a picture is worth a thousand words"; writing amplifies ability | Dropped from skill: blogging-career advice, "writing is a life skill". Kept: report-writing rules (diagrams-first, no giant code pastes) |

## What was deliberately dropped (and why)

Not article content is about agent-relevant method; the skill keeps only what changes agent behavior:

- Career/motivation framing ("underlying fundamental programmer skill", "reading code teaches you experience") — audience was the human learner.
- Blogging-as-writing-practice and long-term English/writing skill advice — life advice, not a reading workflow.
- "Output is feedback that improves learning" as a learning-theory argument — retained as a rule (produce artifacts), dropped as a rationale.

## Ground-truth vs static analysis

The single most important conversion decision: the original emphasizes scenario analysis because *observed execution paths are ground truth*, while static reading is hypothesis. For an agent this matters because its conclusions otherwise lean heavily on speculative pattern-matching.

Rule: every load-bearing conclusion in the report must be labeled either

- **[verified]** — observed via run/debug/test/scenario repro, or
- **[static]** — inferred from reading only.

If most conclusions would be `[static]`, either build the environment (Phase 1) or say so plainly in the report.