# Product Map

## 核心目标

为各 AI 客户端（Codex / WorkBuddy / Claude / Cursor / 豆包）提供一套低缺陷、可维护、
低 Token 消耗的统一任务工作流：先读规则和产品地图再动手，用源码索引定位文件，
按需读取源码，改完同步地图。

## 业务清单

- 每轮先读取完整 Skill 和最新 product-map。
- 需求先识别边界、异常、失败回退和空输入。
- 用源码索引定位到文件，单次最多读 3 个，已读未变不复读。
- 交付前完成验证、清理冗余并同步地图。

## 功能节点

- 工作流规则：`SKILL.md`
- 使用与安装说明：`README.md`
- 变更记录：`CHANGELOG.md`
- 持续维护地图：`product-map.md`（本文件）
- Codex 界面元数据：`openai.yaml`（根目录）、`agents/openai.yaml`（同内容，兼容两种目录约定）
- 许可：`LICENSE`

## 源码索引

本项目是**纯文档型 Skill，没有可执行代码**，所以索引规模天然很小；
但结构仍需登记，否则接手者仍要翻目录。

| 路径 | 职责 | 触发场景 | 关键符号 | 状态 |
|---|---|---|---|---|
| `SKILL.md` | 工作流规则正文：执行顺序、产品地图协议、源码索引协议、防缺陷规则、精简规则、交付契约 | 改规则 / 调索引协议 / 加防缺陷条目 / 改交付要求 | `Source index protocol`、`On-demand read protocol`、`Symptom → file table`、`Index prohibitions`、`Index maintenance`、`Completion contract` | 已验证 |
| `README.md` | 面向使用者：极简安装（一行命令 + Gitee/GitHub 两源 + 给 AI 的安装提示词）+ 价值论述（是什么/为什么装/目标/解决什么问题/价值） | 改安装方式 / 换源 / 改价值表述 / 改 AI 提示词 | `## 快速安装`、`## 为什么很有必要装`、`## 价值在哪` | 已验证 |
| `CHANGELOG.md` | 按日期记录规则变更 | 每次改 `SKILL.md` 或 `README.md` 时 | 日期小节 + `Added` / `Updated` | 已验证 |
| `product-map.md` | 本文件：地图主体 + 源码索引 + 坑点沉淀 | 每轮开始读、每次改动后同步 | `## 源码索引`、`## 历史坑点` | 已验证 |
| `openai.yaml` | Codex 界面元数据（显示名、简述、默认提示） | 改展示名 / 改默认提示语 | `interface.display_name`、`interface.default_prompt` | 已实现 |
| `agents/openai.yaml` | 同上，兼容 `agents/` 目录约定；与根目录那份内容一致（仅换行符不同） | 改元数据时两处同步 | 同上 | 已实现 |
| `LICENSE` | MIT 许可 | 基本不用动 | — | 已实现 |

> 没有共享模块，故未加 `被依赖` 列。改 `SKILL.md` 的规则时，`README.md` 与
> `CHANGELOG.md` 属于必须同步项，见下方「历史坑点」。

## 约束黑名单

- 不使用浏览器模拟人工操作执行任务。
- 不吞掉错误，不直接信任外部输入或接口返回。
- 不做无关遍历、扩展或重复造轮子。
- 不在源码索引里写密钥、token、绝对机器路径。

## 禁用写法

- 空 `catch {}`。
- 未校验的 `any`、`null`、`undefined`、`NaN` 运算。
- 把业务逻辑堆进 UI 回调。
- 索引表里写"从文件名就能看出用途"这类空话。

## 复用组件

- 优先使用后端 API、连接器、CLI、文件和数据库操作。
- 沿用现有 Skill 结构和 Markdown 规范。

## 历史坑点

### 同步与发布

- **git 对 github.com 配了本地代理 `127.0.0.1:7890`，该代理常不在运行**，
  直接 `git clone/push` 会报 `Failed to connect to 127.0.0.1 port 7890`。
  解法：命令前加 `-c "http.https://github.com.proxy="` 清空该配置。
- `github.com` 的 git 端口可能整体不通，但 `api.github.com` 与
  `codeload.github.com` 正常。备选路径：用 codeload 拉 tarball；
  推送走 GitHub Git Data API（blobs → trees → commits → 更新 ref）。
- **`gh api -f tree=<json>` 会把嵌套 JSON 当表单字段**，报
  `Invalid tree info (HTTP 422)`。必须用 `--input -` 传 JSON body。
- **`git ls-tree` 默认把非 ASCII 路径转义成 `\345\220\257` 八进制**。
  按未转义路径比对会造成"本地新增 + 远端删除"的误判，曾因此把中文名文件从远端删掉。
  解法：`git -c core.quotepath=false ls-tree -r HEAD`。用脚本改远端文件树前，
  先核对"删除"列表里有没有中文名。
- **Gitee 拒绝浅克隆推送**：`shallow update not allowed`。先 `git fetch --unshallow`。
- **Gitee 新建仓库时 `private:false` 不生效**（默认私有）。且空仓库无法 PATCH 分支
  （报 `The repository does not have the branch`）。正确顺序：建仓 → 先推一次代码 →
  再 PATCH 设 `private:false`（PATCH 必须带 `name`，否则 400）。

### 环境

- `quick_validate.py` 可能因环境缺少 `PyYAML` 失败，需报告环境缺口并执行可用的替代结构校验。
- GitHub App 可能返回 `403 Resource not accessible by integration`；同步前必须确认 Contents 写权限。

### 规则一致性

- **改了 `SKILL.md` 的规则，必须同步 `README.md` 与 `CHANGELOG.md`**，
  否则人读版本和 AI 读版本会分叉，出现"文档说一套、规则做另一套"。
- 本 Skill 早期版本只有索引表格格式、没有"怎么用索引"，AI 仍可能一次读全量；
  2026-11-02 补入按需读取协议后解决。改协议时不要把它降级回纯格式说明。

## 输出格式

- 简洁说明结果、关键文件、验证状态和真实阻塞。
- 生成代码时附业务校验清单和 3-5 条风险清单。
- 交付时列出「涉及文件」：实际读过或改过的每个文件。

## Token-节约规则

- 只读取任务相关节点和文件片段，单次最多 3 个文件且写明理由。
- 优先复用成熟方案，删除无关素材、废弃注释和冗余代码。
- 一次闭环交付，避免重复定义和无效解释。
