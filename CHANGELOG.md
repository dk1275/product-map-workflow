# Changelog

## 2026-11-23

### Added

- **新增「交接日志协议」（Handoff log protocol，强制）**，补掉 `agent-handoff.md` 长期缺字段的问题：
  - 每条记录必须带 **秒级时间戳 + 时区**（`2026年11月23日 17点52分25秒（北京时间 UTC+8）`）；
    只写日期、或写"刚刚 / 今天稍早 / 昨天 / 上一轮"一律视为不合格。跨时区时两处都写。
  - 必填字段固化为 9 项：本次目标与范围、已完成（带产物路径 / 提交号 / ID）、**整体目标达成度
    （必须带分母）**、下一阶段第一批具体动作、**剩余工作按技术线 / 美术线分列**、给下一位 Agent
    的直读指令、验证边界（已验证 / 未验证 / 待用户验收）、阻塞与最小解除条件、涉及文件。
  - 结构固定为「顶部状态快照（覆盖式）+ 按时间追加记录（只增不改）」；接手者只读快照 +
    最近 1–3 条 + 对应源码索引行，不通读全文，旧结论留作历史。
  - 给出「给下一位 Agent 测试」四条判据：目标与已证明项、下一条唯一动作及具体文件、
    禁止项与回归定义、用户待验收或待决策项；出现"按上次那样 / 你懂的 / 用户说的那个文件"即不合格。
  - 交付契约收紧：**没有交付时刻新写的那条交接记录，视为交付未完成**。
- 产品地图强制章节增加 `交接记录`（一行：交接文件路径 + 最新记录时间戳 + 验收状态，覆盖式，不累积）。

### Updated

- `product-map.md` 同步：业务清单、功能节点、源码索引、历史坑点。
- `README.md` 新增「交接靠人传话」一节：必填项 vs 不合格写法对照表、时间戳与达成度写法示例。
- 版本 `1.0.0` → `1.1.0`（新增协议，向后兼容）。
- 执行顺序：第 2 步补读交接快照与最近记录，第 7 步补写交接记录；常驻偏好段与交付契约同步收紧。
- `openai.yaml` / `agents/openai.yaml` 默认提示同步：从「minimal output」改为执行后必须补一条带时间戳的交接记录。

### Why

- 实测痛点：`agent-handoff.md` 只有「当前目标 / 阶段 / 已完成 / 下一步」这类粗字段，
  缺时间戳、缺达成度分母、缺美术 / 技术分工、缺下一位 agent 的直读指令，
  换 agent 时仍要用户口述上下文，交接等于没交接。
- 与 `~/.agents/RESIDENT_RULES.md` §3 的四项必填（达成度带分母、下一阶段第一批动作、
  剩余美术 / 技术工作、给下一位 agent 直读指令）对齐，避免两处规则分叉。

## 2026-11-12

### Fixed

- 将“每轮读取完整 Skill catalog”收窄为只读取常驻规则和任务相关技能，避免无关上下文消耗。
- 明确产品地图是公共性质的源码索引，不是流水账；过程记录应放入 `agent-handoff.md` 或项目工作日志。

## 2026-08-11

### Added

- 增加后端优先规则。
- 禁止使用浏览器模拟点击、输入、拖拽、上传等人工操作。
- 优先使用连接器、API、CLI、文件/数据库操作和结构化资料整理。

## 2026-08-17

### Updated

- README 增加 Bug 优先、防御式编程、后端优先和安装说明。
- README 明确代码交付必须包含业务校验清单与风险清单。
- 增加最小 product-map，记录本 Skill 的目标、约束、复用方案和历史坑点。

## 2026-11-02

### Added

- **源码索引（`源码索引`）升为产品地图强制章节**，索引表改为
  `路径 / 职责 / 触发场景 / 关键符号 / 状态` 五列。
  新增「触发场景」列：什么症状才值得读这个文件，没有匹配就不读。
- 新增 **按需读取协议（On-demand read protocol，强制）**：禁止整目录索要；
  单次最多 3 个文件且写明理由；已读未变不复读；不做投机性依赖拉取。
- 新增 **症状 → 文件表（Symptom → file table）**：症状 / 先查 / 再查，
  保证第一次就读对文件。
- 新增 **索引禁止行为（Index prohibitions）**：整目录请求、读静态资源与构建产物、
  重复索要、以防万一拉依赖、索引写密钥与绝对路径。
- 交付契约要求列出「涉及文件」：实际读过或改过的每个文件。

### Updated

- 执行顺序第 6 步：改文件时必须同步新增/更新对应的源码索引行。
- 交付契约：确认源码索引覆盖本次任务触碰的每个文件，无缺失行、无已删文件的行。
- 索引维护规则收紧：超约 15 个源文件按 `功能节点` 分组；索引与功能节点必须对齐；
  每格一行；同任务内同步，过期索引比没有更糟。
- README 补完整中文索引规范、按需读取协议与 WorkBuddy 安装路径。
- `product-map.md` 自身按新规补齐源码索引（此前缺这一节），并沉淀同步与发布坑点：
  git 代理 7890 未运行的绕过、GitHub Git Data API 推送、`core.quotepath=false`
  与中文路径、Gitee 浅克隆推送被拒、Gitee 建仓先私有后 PATCH 转公开。

### Fixed

- 早期版本只有索引表格格式、没有"怎么用索引"，AI 仍可能一次读全量；
  本次补入按需读取协议后解决。改协议时不要降级回纯格式说明。

## 2026-11-02（发布到 Gitee）

### Added

- 建立 Gitee 镜像仓库 `dk1275/product-map-workflow`（公开），与 GitHub 双端同步。

## 2026-11-02（README 第一版：收敛为纯安装文档）

### Updated

- README 收敛为**只讲安装**，规范细节全部留在 `SKILL.md`，避免两处规则分叉。
- 新增「给 Agent 的统一安装方式」：判平台 → 判客户端 → 建父目录 → 先试 Gitee 再换
  GitHub → 校验 frontmatter → 已存在则 pull → 报告路径/来源/提交号。
  明确「成功标准只有 SKILL.md 可读且 frontmatter 完整」，不许拿 clone 退出码当成功。
- 分平台给命令：macOS / Linux 用终端，Windows 用 PowerShell（含反引号续行提醒）。
- 每个平台补**无 git 降级方案**：Gitee 走 `archive/main.zip`，GitHub 走 `codeload` tar/zip。
- 排障补充实测错误：`Failed to connect to 127.0.0.1 port 7890`（本机代理配置）、
  `CONNECT tunnel failed, response 502`、`Recv failure: Operation timed out`。

### Verified

- 实测通过：Gitee `git clone`、Gitee `archive/main.zip` + `cp -R`、
  GitHub `codeload` tar + `--strip-components=1`。
- 实测失败并写入排障：GitHub `git clone`（代理未运行时报 502，清空代理后超时）。

## 2026-11-02（README 第二版：先安装，再讲价值）

### Updated

- **调整叙述顺序**：开头就是极简安装（macOS/Linux 一行、Windows PowerShell 一行），
  再给两个源各自的地址与「可直接丢给 AI 的安装提示词」，价值论述放到安装之后。
  第一版只讲安装、不讲为什么值得装，读者装完仍不知道这东西解决什么问题。
- 新增价值论述：**这个 Skill 到底干嘛的** / **为什么很有必要装** /
  **目标** / **它解决什么问题**（7 行对照表）/ **价值在哪**（省钱 / 少错 / 可沉淀 / 不跑偏）。
- 安装提示词写明验收标准（`SKILL.md` + frontmatter + `name`），并禁止 AI 在验证失败时
  说"装好了"；GitHub 版额外加一句「连不上别重试超过两次，直接换 Gitee」。
- 明确 Gitee 为推荐源，附原因（GitHub git 通道在部分网络不通）。

### Verified

- 实测 macOS 一行命令：`mkdir -p ~/.workbuddy/skills && git clone <Gitee> <目标>` 通过，
  装出 8 个文件且 frontmatter 完整。
- 实测 Gitee `archive/main.zip` + `cp -R` 降级方案通过。

## 2026-11-02（v1.0.0 · 适配 WorkBuddy 技能市场上架）

### Added

- frontmatter 按开放平台「技能基础结构」补齐必备字段：
  `display_name`（Token 省钱大师）、`display_name_en`（Token Saver）、
  `description`（含中文触发词）、`description_zh`、`description_en`、
  `version`（1.0.0）、`author`。
- 可选字段 `examples_zh` / `examples_en` 各 3 条，用于市场详情页【试试这样用】。
- 正文开头补一段中文「解决什么」，给市场审阅者和用户看。

### Changed

- `description` 改为中英混合并加入触发词（省 token、上下文爆了、乱翻代码、
  源码索引、换 AI 接不上…），提升中文场景命中率。
- 上传包结构收敛为官方要求的最小形态：`product-map-workflow/SKILL.md`，
  不含 README 等其他文件，避免解析失败。

### Note

- 官方结构支持的可选目录 `references/` / `scripts/` / `templates/` 本技能均未使用，
  规则全部在 SKILL.md 内，减少 AI 漏读引用的风险。
- README 保留在 Git 仓库，不进入上架包。
