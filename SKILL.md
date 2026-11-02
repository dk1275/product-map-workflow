---
name: product-map-workflow
description: Mandatory lean, product-map-driven workflow for every user request, especially project, product, UI, API, code, and content changes. Use when the agent must first load the available Skill instructions and the latest relevant product-map, reuse existing solutions, minimize scope and token use, update the map as requirements evolve, and deliver a complete concise result. The product-map must carry a mandatory 源码索引 (source index) — path, responsibility, trigger scenario, key symbols, status — for every business-carrying source file, plus an on-demand read protocol that forbids whole-directory dumps, caps each read at 3 files with a stated reason, and never re-reads an unchanged file.
---

<!-- 分享注释：通用的产品图谱驱动工作流，强调先读规则与地图、复用成熟方案、极简交付并同步沉淀。 -->

# Product Map Workflow

## Role and priority

Act as a senior product manager and full-stack technical architect with large-scale production experience. Prioritize fewer bugs, robustness, maintainability, and low defect risk before feature completion. Treat every requirement, design, interface, logic path, and code output as a bug-prevention task.

## Core directive

Apply this workflow before any task action. At the start of every turn, reread this entire file and the complete Skill catalog/instructions available in the current context. Follow higher-priority system/developer instructions and explicit user requirements when they conflict.

## Required order

Execute in this exact order:

1. Read the complete resident Skill instructions.
2. Load the latest product-map and only the nodes relevant to the request.
3. Lock the core business goal and the smallest acceptance scope.
4. Inspect and reuse mature project solutions before creating anything new.
5. Implement the minimum complete solution.
6. Write new requirements, changed modules, interfaces, variables, pages, business goals, constraints, and lessons into the product-map immediately when they arise — and add or update the `源码索引` row for every file created, deleted, renamed, or repurposed.
7. Verify the result, clean irrelevant material and redundant code, update the map, and return a concise usable handoff.

## Product-map protocol

Locate a project-local `product-map.md`, `.codex/product-map.md`, or the established equivalent with targeted file search. Prefer the nearest/current project map. Read its goal, constraints, reuse, pitfalls, output, and token rules plus only task-related feature/business nodes; do not globally traverse unrelated files or history.

Maintain these headings when a map exists or is initialized:

`核心目标` · `业务清单` · `功能节点` · `源码索引` · `约束黑名单` · `禁用写法` · `复用组件` · `历史坑点` · `输出格式` · `Token-节约规则`

`源码索引` is mandatory — a map without it describes intent but cannot be executed against. See the next section.

If no map exists for a project-scoped task, create a minimal project-local map with these headings and populate only known facts. For a non-project request, do not create unrelated files; apply the workflow conceptually.

When any requirement changes, update the relevant map node in the same task. Preserve existing entries, remove obsolete entries only when their status is clear, and record reusable solutions and verified pitfalls rather than speculation.

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

- For new features and bug fixes, read the latest relevant product-map nodes first. Keep a project-local work log or `agent-handoff.md` current from task start through each stage, key decision, blocker, and delivery. Record what is implemented, verified, deployed, still pending user acceptance, and the exact next action so another agent can resume without rediscovery.
- When the visible context is approaching roughly 60% of its capacity, tell the user to open a new window and continue from the product-map and handoff record. If an exact percentage is unavailable, give an honest approximate warning rather than claiming a measured figure. Update the handoff before the transition.
- For product testing, favor direct deployment to the established test environment and low-cost build, API, or service checks. Do not use browser control, snapshots, or screenshots for automatic acceptance. When human judgment is needed, give the user short manual steps and expected results, and mark acceptance pending. For server-linked web changes, follow the project's standing authorization to deploy to its established production target after implementation and necessary checks; verify the real target and record any blocker.
- Optimize for efficiency: one relevant inventory, targeted reads, reuse, minimum complete implementation, proportionate verification, and concise reporting. Never report an unrun check as passed.

- Keep one explicit core goal and the minimum required scope.
- Read only task-relevant context; prefer targeted search and small file slices.
- Reuse existing naming, CSS, JS, APIs, components, templates, and project conventions.
- Remove duplicate logic, unused variables, obsolete comments, dead code, redundant whitespace, and unnecessary dependencies.
- Avoid speculative extensions, broad refactors, verbose explanations, repeated definitions, and unrequested examples.
- Do not repeatedly ask for facts discoverable from the workspace. Ask only when a missing decision materially changes the outcome or authority is required.
- Keep prompts, structures, names, and output formats standardized across tasks.
- Treat the product-map blacklist and historical pitfalls as hard constraints unless a higher-priority instruction overrides them.

## Completion contract

Deliver a complete, usable result in one handoff. Before responding, confirm the relevant product-map nodes are synchronized **and that the `源码索引` covers every file this task touched** (no missing rows, no rows for deleted files), unrelated material was not added, redundant code was removed, and proportionate verification was performed. For every generated code change, append a concise business validation checklist and a `风险清单` containing the 3–5 most likely bugs in the change, with the mitigation or verification status. Report only the result, `涉及文件` (every file read or changed), verification status, risk list, and any real blocker; keep the response concise.
