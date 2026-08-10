---
name: product-map-workflow
description: Mandatory lean, product-map-driven workflow for every user request, especially project, product, UI, API, code, and content changes. Use when Codex must first load the available Skill instructions and the latest relevant product-map, reuse existing solutions, minimize scope and token use, update the map as requirements evolve, and deliver a complete concise result.
---

<!-- 分享注释：通用的产品图谱驱动工作流，强调先读规则与地图、复用成熟方案、极简交付并同步沉淀。 -->

# Product Map Workflow

## Core directive

Apply this workflow before any task action. At the start of every turn, reread this entire file and the complete Skill catalog/instructions available in the current context. Follow higher-priority system/developer instructions and explicit user requirements when they conflict.

## Required order

Execute in this exact order:

1. Read the complete resident Skill instructions.
2. Load the latest product-map and only the nodes relevant to the request.
3. Lock the core business goal and the smallest acceptance scope.
4. Inspect and reuse mature project solutions before creating anything new.
5. Implement the minimum complete solution.
6. Write new requirements, changed modules, interfaces, variables, pages, business goals, constraints, and lessons into the product-map immediately when they arise.
7. Verify the result, clean irrelevant material and redundant code, update the map, and return a concise usable handoff.

## Product-map protocol

Locate a project-local `product-map.md`, `.codex/product-map.md`, or the established equivalent with targeted file search. Prefer the nearest/current project map. Read its goal, constraints, reuse, pitfalls, output, and token rules plus only task-related feature/business nodes; do not globally traverse unrelated files or history.

Maintain these headings when a map exists or is initialized:

`核心目标` · `业务清单` · `功能节点` · `约束黑名单` · `禁用写法` · `复用组件` · `历史坑点` · `输出格式` · `Token-节约规则`

If no map exists for a project-scoped task, create a minimal project-local map with these headings and populate only known facts. For a non-project request, do not create unrelated files; apply the workflow conceptually.

When any requirement changes, update the relevant map node in the same task. Preserve existing entries, remove obsolete entries only when their status is clear, and record reusable solutions and verified pitfalls rather than speculation.

## Lean execution rules

- Keep one explicit core goal and the minimum required scope.
- Read only task-relevant context; prefer targeted search and small file slices.
- Reuse existing naming, CSS, JS, APIs, components, templates, and project conventions.
- Remove duplicate logic, unused variables, obsolete comments, dead code, redundant whitespace, and unnecessary dependencies.
- Avoid speculative extensions, broad refactors, verbose explanations, repeated definitions, and unrequested examples.
- Do not repeatedly ask for facts discoverable from the workspace. Ask only when a missing decision materially changes the outcome or authority is required.
- Keep prompts, structures, names, and output formats standardized across tasks.
- Treat the product-map blacklist and historical pitfalls as hard constraints unless a higher-priority instruction overrides them.

## Completion contract

Deliver a complete, usable result in one handoff. Before responding, confirm the relevant product-map nodes are synchronized, unrelated material was not added, redundant code was removed, and proportionate verification was performed. Report only the result, key files or artifacts, verification status, and any real blocker; keep the response concise.
