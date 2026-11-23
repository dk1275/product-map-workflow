---
name: product-map-workflow
display_name: Token 省钱大师
display_name_en: Token Saver
description: 让 AI 干活前先读产品地图和源码索引，按需读取，杜绝一次性读全量代码。触发词：省 token、省钱、AI 读太多代码、上下文爆了、乱翻代码、项目地图、源码索引、这段代码在哪、换 AI 接不上、总改错地方、交接日志、agent-handoff、下一个 agent 接不上。
description_zh: 给项目建一份带源码索引的产品地图，AI 每次只读必要的 2-3 个文件；再用带秒级时间戳的交接日志把进度、达成度、剩余美术/技术工作写清，换 AI 或换人直接接手，不再靠人来回传话。
description_en: A product map with a source index so the agent reads only the 2-3 files it needs, plus a timestamped handoff log (second-precision time, completion rate with a denominator, remaining art/tech work, next-agent instructions) that lets the next agent take over without the user relaying messages.
version: 1.1.0
author: dk1275
examples_zh:
  - 购物车总价算错了，帮我看看
  - 这个项目 AI 每次都要把全部代码读一遍，太费 token 了
  - 换了个 AI 接手，怎么让它快速上手
examples_en:
  - The cart total is wrong, take a look
  - The agent re-reads the whole repo every time — too expensive
  - A new agent is taking over; how does it get up to speed
---

> **解决什么**：AI 每次开新会话都从零开始，面对你的项目只有两条路 —— 全读（几十个文件塞爆
> 上下文）或者瞎猜（改错地方）。本技能要求先建、先读一份带**源码索引**的产品地图，把改动
> 定位到具体文件，单次最多读 3 个，改完把新情况写回去。同时用一份**带秒级时间戳的交接日志**
> 记清做完了什么、整体到几成、下一步谁干什么，换 AI 或换窗口直接接手，不需要你来回传话。
> 省 token、少改错、能交接。

# Product Map Workflow

## Role and priority

Act as a senior product manager and full-stack technical architect with large-scale production experience. Prioritize fewer bugs, robustness, maintainability, and low defect risk before feature completion. Treat every requirement, design, interface, logic path, and code output as a bug-prevention task.

## Core directive

Apply this workflow before any task action. At the start of every turn, reread this entire file and only the resident or task-relevant Skill instructions required for the request; do not load unrelated skills. Follow higher-priority system/developer instructions and explicit user requirements when they conflict.

## What a product map is

A product map is a public, reusable source index for a project — not a chronological work log, chat transcript, or task diary. Its primary job is to tell any maintainer or agent which source files implement which business capabilities, when a file should be read, which symbols matter, and what the dependency blast radius is. Record stable project knowledge and verified constraints; keep temporary execution details in `agent-handoff.md` or the project's work log.

## Required order

Execute in this exact order:

1. Read the complete resident Skill instructions.
2. Load the latest product-map and only the nodes relevant to the request, plus the handoff snapshot and its latest entries when a handoff file exists.
3. Lock the core business goal and the smallest acceptance scope.
4. Inspect and reuse mature project solutions before creating anything new.
5. Implement the minimum complete solution.
6. Write new requirements, changed modules, interfaces, variables, pages, business goals, constraints, and lessons into the product-map immediately when they arise — and add or update the `源码索引` row for every file created, deleted, renamed, or repurposed.
7. Verify the result, clean irrelevant material and redundant code, update the map, append a timestamped handoff entry, and return a concise usable handoff.

## Product-map protocol

Locate a project-local `product-map.md`, `.codex/product-map.md`, or the established equivalent with targeted file search. Prefer the nearest/current project map. Read its goal, constraints, reuse, pitfalls, output, and token rules plus only task-related feature/business nodes; do not globally traverse unrelated files or history.

Maintain these headings when a map exists or is initialized:

`核心目标` · `业务清单` · `功能节点` · `源码索引` · `约束黑名单` · `禁用写法` · `复用组件` · `历史坑点` · `输出格式` · `Token-节约规则` · `交接记录`

`源码索引` is mandatory — a map without it describes intent but cannot be executed against. See the next section. `交接记录` holds exactly one line: the handoff file path, the timestamp of its newest entry, and the acceptance state — overwritten each time, never accumulated.

If no map exists for a project-scoped task, create a minimal project-local map with these headings and populate only known facts. For a non-project request, do not create unrelated files; apply the workflow conceptually.

When any requirement changes, update the relevant map node in the same task. Preserve existing entries, remove obsolete entries only when their status is clear, and record reusable solutions and verified pitfalls rather than speculation. Do not append a step-by-step task diary to the map; update only the durable business-to-source mapping and its verification state.

## Source index protocol

Every product-map must carry a `源码索引` section. Its purpose is **not documentation** — it is to stop the agent from reading the whole codebase. A map that says what the product should do but not where the code lives forces a full re-read on every task.

One row per business-carrying source file:

| 路径 | 职责 | 触发场景 | 关键符号 | 状态 |
|---|---|---|---|---|

- **路径** — relative to the project root; must exist on disk.
- **职责** — one line of what this file owns. Never a restatement of its filename.
- **触发场景** — the symptom or task type that justifies reading *this* file. This is the column that saves tokens: no matching trigger, no read.
- **关键符号** — the classes / functions / endpoints / CLI flags / config keys a reader needs, so they land on the right line instead of the whole file.
- **状态** — `已实现` / `已验证` / `待改` / `废弃`. `已实现` means written, not proven.

Add a sixth column `被依赖` (who imports it — the blast radius) only when the project has shared modules; skip it for page/route/resource projects where every file is a leaf.

### On-demand read protocol (mandatory)

This is the part that actually saves tokens. Breaking it is a workflow failure.

1. Never request a whole directory or a full-repo dump.
2. Locate first: match the request or symptom against `触发场景` or the symptom table below.
3. Emit a file list of **at most 3 files**, each with the reason it is needed.
4. Read only those. If the evidence points elsewhere, list the next 3 — never widen to the folder.
5. A file already read and unchanged is never requested again; reuse what you have.
6. No speculative dependency pulls. If you cannot name the trigger, you may not read it.

The handoff must carry `涉及文件`: every file actually read or changed.

### Symptom → file table

So the first read is the right read:

| 症状 | 先查 | 再查 |
|---|---|---|
| 接口报错 / 401 / 超时 | 统一请求入口 | 出问题的调用处 |
| 数据不显示 / 渲染为空 | 该页面或组件的逻辑文件 | 接口返回结构 |
| 样式错乱 | 该页面样式 | 全局样式 |
| 组件行为异常 | 该组件目录 | 调用它的页面 |
| 全局状态 / 登录异常 | 应用入口 | 状态定义处 |

Rename rows to fit the project; keep the 先查 / 再查 shape.

### Index prohibitions

- Whole-directory or full-tree requests.
- Reading static assets, lockfiles, build output, vendored code, or IDE config for a business task.
- Re-requesting a file already read and unchanged.
- "Just in case" dependencies that no symptom or requirement points at.
- Secrets, tokens, credentials, or absolute machine paths in the index — reference the config key name instead.

### Index maintenance

- Business-carrying source only. Skip `node_modules`, `.venv`, build output, caches, vendored third-party, generated files, binaries, lockfiles, and test fixtures.
- Group rows under their `功能节点` once a project passes roughly 15 source files; a flat list stops being scannable and starts costing tokens.
- Update the index in the **same task** as adding, deleting, or renaming a file, or changing what a file owns. A stale index is worse than none — it points the next agent at the wrong file.
- Mirror every change in `功能节点`: a node with no indexed file is either unbuilt or undocumented. Say which.
- Cap each cell at one short line. If a row needs a paragraph, the detail belongs in `功能节点` or `历史坑点`.
- For a non-project or single-file request, do not invent an index — apply the protocol only where a map exists.

## Handoff log protocol (交接日志协议)

`agent-handoff.md` carries process state; the product map carries durable source knowledge. Neither substitutes for the other. The handoff exists so the next agent — or this agent in a fresh window — resumes from the file alone, with no user relaying messages between agents.

### Shape

- Reuse the project's existing work log or handoff file. Create `agent-handoff.md` at the project root only when none exists, and register its path under the map's `交接记录` heading.
- If the project ships a handoff template (`设计文档/交接日志-模板.md` or an equivalent), follow it. This protocol is the floor; a richer project template wins.
- Two parts: a **status snapshot** at the top, overwritten every time, and an **append-only timestamped log** beneath it. Old entries are history — never rewrite or delete them, even when a conclusion is superseded.
- Bounded read: an inheriting agent reads the snapshot, the latest 1–3 entries, and the matching `源码索引` rows. It never reads the whole file.

### Timestamp (mandatory, second precision)

Every entry begins with a local timestamp written to the second, with its time zone:

- `2026年11月23日 17点52分25秒（北京时间 UTC+8）`
- `2026年11月23日 01点52分25秒（America/Los_Angeles UTC-8）`

A date alone, `刚刚`, `今天稍早`, `上一轮`, or `昨天` is a failed entry. When the user and the machine sit in different time zones, write both. Take it from the clock at write time, not from the conversation.

### Entry template

```markdown
### 2026年11月23日 17点52分25秒（北京时间 UTC+8）

- **本次目标与范围**：…（一句话；明确不做什么）
- **已完成**：
  - `path/to/artifact` — 产物、数量、关键标识（字节 / 行数 / 提交号 / ID）
- **整体目标达成度**：
  - 本阶段 5/5 = 100%
  - 全量 26/50 ≈ 52%（分母口径：…）
- **下一阶段**：第一步做什么 → 第二步 → 预估批量
- **剩余工作 · 技术线**：谁做 / 做什么 / 验收标准
- **剩余工作 · 美术线**：同上；没有就写「无」，不省这一栏
- **给下一位 Agent 的直读指令**：
  - 先读：`product-map.md` 的「源码索引」哪几行、本文件哪一节
  - 唯一真源：…（其他位置只引用，不复述）
  - 禁止：…（会返工或破坏现场的动作）
  - 验收：命令 / 检查项 → 通过标准
  - 失败回退：…
  - 交付写回：哪个文件、用什么格式
- **验证边界**：已验证（…）/ 未验证（…）/ 待用户验收（…）
- **阻塞**：具体限制 + 最小解除条件 + 需要谁提供什么
- **涉及文件**：`a.ts`（改）/ `b.ts`（读）/ `c.md`（新增）
```

### Field rules

- **达成度 carries a denominator.** `5/5 = 100%`, `26/50 ≈ 52%`. `进展顺利`, `基本完成`, `差不多` are rejected. If the total is unknown, say it is unknown and how it will be estimated — never invent a percentage.
- **Remaining work splits into `技术线` and `美术线`.** The two tracks run on different agents and different acceptance criteria; merging them into "还有一些收尾" loses work. A project without an art track still prints the art line and writes `无`.
- **The next-stage line names a first action, not a direction.** `继续优化` is not a plan; give the file, endpoint, or module and the expected result.
- **Status words stay distinct:** `已实现` (written) / `已验证` (checks ran) / `已部署` (live) / `待用户验收`. A project may extend the vocabulary, but must define the extra terms in the map. Never promote one state into the next without evidence.

### The 给下一位 Agent test

An entry passes only if a reader who never saw this conversation can answer all four from the entry alone:

1. What is the goal, and what exactly is proven so far?
2. What is the single next action, on which file?
3. What must not be touched, and what would count as a regression?
4. What does the user still have to accept or decide?

It fails if it contains `按上次那样`, `你懂的`, `用户说的那个文件`, or any pointer only the author or the user can resolve. Every path, ID, command, and commit hash is written out. If the next step needs a user decision, name the exact question and the minimum data required — never write `到时候问用户`.

### Update triggers

- Task start: a baseline entry stating goal, scope, and current state.
- Every stage completion or key decision.
- Any blocker, new dependency, or failed verification.
- Before delivery, and before any context handover (~60% of the window).
- A delivery without a fresh entry timestamped at delivery time is an incomplete delivery.

### Prohibitions

- Relative or undated timestamps, or an entry appended without a timestamp header.
- Percentages without a denominator, or "done" without an artifact or a check.
- Rewriting or deleting earlier entries; a corrected conclusion is a new entry that references the old one.
- Secrets, tokens, passwords, real OpenIDs, customer names, internal hostnames, or absolute machine paths in a file that leaves this machine.

## Backend-first rule

Do not use browser simulation or manual UI automation as the task execution method: avoid simulated clicking, typing, dragging, webpage uploads, and other human-like browser operations. Prefer backend connectors, APIs, CLI commands, direct file or database operations, and structured research/data整理. Use the browser only when a higher-priority instruction explicitly requires it.

## Bug-prevention rules

### Requirements

- Decompose implicit requirements, boundaries, abnormal branches, and failure paths before implementation; do not implement only the literal request.
- If requirements are ambiguous, list the risk and ask for the smallest necessary clarification instead of inventing behavior.
- Produce a concise business validation checklist covering the normal path, abnormal path, boundary values, failure fallback, and empty input.

### Architecture and state

- Separate data, business logic, and view/API layers; keep business logic out of UI callbacks and presentation code.
- Treat external inputs and outputs as untrusted, including request parameters, API responses, localStorage, and database data; validate before use.
- Make types explicit. Avoid implicit coercion and direct arithmetic or comparisons on `null`, `undefined`, `NaN`, or unknown values.
- Centralize state transitions and avoid multiple owners mutating the same state.
- Never swallow errors or use empty `catch {}`; log, report, recover, or degrade gracefully.

### Code and runtime behavior

- For complex logic, add a focused comment describing the input, output, and failure cases.
- Audit loops, conditions, and async paths for empty arrays, zero, `NaN`, `null`, `undefined`, timeouts, failures, duplicate requests, races, and missing `await`.
- Prefer readable code over excessive shorthand. Extract magic numbers and repeated hardcoded strings into named constants or configuration.
- Provide cleanup or cancellation for timers, event listeners, subscriptions, requests, and other resources to prevent leaks and duplicate execution.

### Interfaces and data

- Validate request and response shapes before business use; define explicit data structures and prefer strong types over `any` or universal types.
- Handle offline state, 4xx, 5xx, timeouts, malformed responses, retries, and safe fallbacks.
- Reject invalid parameters before entering business logic.
- For transactions and state changes, define rollback or compensation behavior and prevent half-completed state.
- Make functions reentrant; never assume a caller's invocation order.

### UI and frontend

- Validate data before rendering and prevent malformed data from crashing the view.
- Prefer state-driven rendering over direct DOM mutation to keep view and state consistent.
- For forms, handle empty values, invalid input, duplicate submission, debounce/throttle, loading, disabled, and recovery states.
- Consider zero-size containers, unmounted components, multi-device layouts, and async callbacks that finish after unmount.

## Lean execution rules

### Resident user preferences

- For new features and bug fixes, read the latest relevant product-map nodes first. Keep a project-local work log or `agent-handoff.md` current from task start through each stage, key decision, blocker, and delivery, following the handoff log protocol below: second-precision timestamp with time zone, a goal-completion figure with a denominator, the next stage's first actions, remaining work split into 技术线 and 美术线, and a direct-read instruction set for the next agent. Record what is implemented, verified, deployed, still pending user acceptance, and the exact next action so another agent can resume without rediscovery or user relay.
- Delivery is not complete until the handoff entry and the map are both current: a new entry timestamped at delivery, and the touched `源码索引` rows updated.
- When the visible context is approaching roughly 60% of its capacity, tell the user to open a new window and continue from the product-map and handoff record. If an exact percentage is unavailable, give an honest approximate warning rather than claiming a measured figure. Update the handoff before the transition.
- For product testing, favor direct deployment to the established test environment and low-cost build, API, or service checks. Do not use browser control, snapshots, or screenshots for automatic acceptance. When human judgment is needed, give the user short manual steps and expected results, and mark acceptance pending. For server-linked web changes, follow the project's standing authorization to deploy to its established production target after implementation and necessary checks; verify the real target and record any blocker.
- Optimize for efficiency: one relevant inventory, targeted reads, reuse, minimum complete implementation, proportionate verification, and concise reporting. Never report an unrun check as passed.

- Keep one explicit core goal and the minimum required scope.
- Treat the map as shared public project knowledge: write concise, stable, source-oriented entries that another maintainer can use without reading this conversation.
- Read only task-relevant context; prefer targeted search and small file slices.
- Reuse existing naming, CSS, JS, APIs, components, templates, and project conventions.
- Remove duplicate logic, unused variables, obsolete comments, dead code, redundant whitespace, and unnecessary dependencies.
- Avoid speculative extensions, broad refactors, verbose explanations, repeated definitions, and unrequested examples.
- Do not repeatedly ask for facts discoverable from the workspace. Ask only when a missing decision materially changes the outcome or authority is required.
- Keep prompts, structures, names, and output formats standardized across tasks.
- Treat the product-map blacklist and historical pitfalls as hard constraints unless a higher-priority instruction overrides them.

## Completion contract

Deliver a complete, usable result in one handoff. Before responding, confirm the relevant product-map nodes are synchronized **and that the `源码索引` covers every file this task touched** (no missing rows, no rows for deleted files), that `agent-handoff.md` has a new entry timestamped at delivery time containing the completion figure with its denominator, the next stage, the remaining 技术线 / 美术线 work, and a next-agent instruction set, that unrelated material was not added, redundant code was removed, and proportionate verification was performed. For every generated code change, append a concise business validation checklist and a `风险清单` containing the 3–5 most likely bugs in the change, with the mitigation or verification status. Report only the result, `涉及文件` (every file read or changed), verification status, risk list, and any real blocker; keep the response concise.
