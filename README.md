# product-map-workflow

精简的任务执行 Skill：先读规则和产品地图再动手，用**源码索引**定位文件、按需读取，
改完同步地图。

规则正文在 [`SKILL.md`](./SKILL.md)，本文件只讲**怎么装**。

---

## 源

两个源，任选其一，内容一致。

| 源 | 地址 | 说明 |
|---|---|---|
| **Gitee**（推荐） | `https://gitee.com/dk1275/product-map-workflow.git` | 国内直连稳定 |
| GitHub | `https://github.com/dk1275/product-map-workflow.git` | git 通道可能不通，见降级方案 |

## 安装位置

| 客户端 | 目标目录 |
|---|---|
| WorkBuddy / CodeBuddy | `~/.workbuddy/skills/product-map-workflow` |
| Codex | `~/.codex/skills/product-map-workflow` |

Windows 把 `~` 换成 `%USERPROFILE%`（PowerShell 里用 `$env:USERPROFILE`）。

两个客户端都要时，装一份再复制过去，不要重复克隆。

---

## 一、给 Agent 的统一安装方式

照顺序做，不用回头问用户选哪个源。

1. **判平台**
   - `uname -s` 返回 `Darwin` / `Linux` → 走第二章的终端命令
   - PowerShell 里 `$env:OS -eq "Windows_NT"` → 走第三章的 PowerShell 命令

2. **判客户端**
   - 装 WorkBuddy → `~/.workbuddy/skills/`
   - 装 Codex → `~/.codex/skills/`
   - 两个都要 → 先装一份，再整目录复制

3. **建父目录**：目标目录的父级 `skills/` 不存在就先建。

4. **克隆**：**先试 Gitee**，报错再换 GitHub，再不行用降级方案（tar / zip）。

5. **校验（必做）**
   - 目标目录下存在 `SKILL.md`
   - 文件开头是 `---` 开头的 frontmatter，含 `name: product-map-workflow` 和 `description:`
   - 任一不满足 → 安装失败，报告真实报错，**不要凭 clone 退出码判成功**

6. **目录已存在时**
   - 是 git 仓库 → `git pull` 更新
   - 不是 → 先备份再整目录覆盖

7. **报告**：实际路径、来源（Gitee / GitHub / 压缩包）、`git log -1 --oneline` 的提交号

**成功标准只有一条**：`SKILL.md` 可读且 frontmatter 完整。

---

## 二、macOS / Linux（终端）

### 安装

```bash
# Gitee（推荐，先试这个）
mkdir -p ~/.workbuddy/skills
git clone https://gitee.com/dk1275/product-map-workflow.git \
  ~/.workbuddy/skills/product-map-workflow
```

```bash
# GitHub（Gitee 不通时）
mkdir -p ~/.workbuddy/skills
git clone https://github.com/dk1275/product-map-workflow.git \
  ~/.workbuddy/skills/product-map-workflow
```

装给 Codex，把 `~/.workbuddy/skills` 换成 `~/.codex/skills`：

```bash
mkdir -p ~/.codex/skills
git clone https://gitee.com/dk1275/product-map-workflow.git \
  ~/.codex/skills/product-map-workflow
```

### 更新

```bash
git -C ~/.workbuddy/skills/product-map-workflow pull
```

### 降级：没有 git，或两个源都连不上

```bash
# Gitee 压缩包
mkdir -p ~/.workbuddy/skills/product-map-workflow
curl -L https://gitee.com/dk1275/product-map-workflow/repository/archive/main.zip \
  -o /tmp/pmw.zip
unzip -q /tmp/pmw.zip -d /tmp/pmw
cp -R /tmp/pmw/product-map-workflow-main/. ~/.workbuddy/skills/product-map-workflow/
```

```bash
# GitHub 压缩包
mkdir -p ~/.workbuddy/skills/product-map-workflow
curl -L https://codeload.github.com/dk1275/product-map-workflow/tar.gz/refs/heads/main \
  | tar xz -C ~/.workbuddy/skills/product-map-workflow --strip-components=1
```

---

## 三、Windows（PowerShell）

### 安装

```powershell
# Gitee（推荐，先试这个）
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.workbuddy\skills" | Out-Null
git clone https://gitee.com/dk1275/product-map-workflow.git `
  "$env:USERPROFILE\.workbuddy\skills\product-map-workflow"
```

```powershell
# GitHub（Gitee 不通时）
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.workbuddy\skills" | Out-Null
git clone https://github.com/dk1275/product-map-workflow.git `
  "$env:USERPROFILE\.workbuddy\skills\product-map-workflow"
```

装给 Codex，把 `.workbuddy\skills` 换成 `.codex\skills`：

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills" | Out-Null
git clone https://gitee.com/dk1275/product-map-workflow.git `
  "$env:USERPROFILE\.codex\skills\product-map-workflow"
```

> PowerShell 的续行符是反引号 `` ` ``，不是反斜杠。粘不进去就整行连成一行跑。

### 更新

```powershell
git -C "$env:USERPROFILE\.workbuddy\skills\product-map-workflow" pull
```

### 降级：没有 git，或两个源都连不上

```powershell
$dest = "$env:USERPROFILE\.workbuddy\skills\product-map-workflow"
$zip  = "$env:TEMP\pmw.zip"
$tmp  = "$env:TEMP\pmw"
Invoke-WebRequest -Uri "https://gitee.com/dk1275/product-map-workflow/repository/archive/main.zip" -OutFile $zip
Expand-Archive -Path $zip -DestinationPath $tmp -Force
New-Item -ItemType Directory -Force -Path $dest | Out-Null
Copy-Item "$tmp\product-map-workflow-main\*" $dest -Recurse -Force
```

GitHub 源把 `Invoke-WebRequest` 那行换成：

```powershell
Invoke-WebRequest -Uri "https://codeload.github.com/dk1275/product-map-workflow/zip/refs/heads/main" -OutFile $zip
```

---

## 装完检查

```bash
# macOS / Linux
head -5 ~/.workbuddy/skills/product-map-workflow/SKILL.md
```

```powershell
# Windows
Get-Content "$env:USERPROFILE\.workbuddy\skills\product-map-workflow\SKILL.md" -TotalCount 5
```

应看到以 `---` 开头的 frontmatter，里面有 `name: product-map-workflow`。

---

## 常见问题

**`Failed to connect to 127.0.0.1 port 7890`**
git 配了指向 `127.0.0.1:7890` 的代理但代理没开。清掉后重试：

```bash
git -c "http.https://github.com.proxy=" clone <地址> <目标目录>
```

```powershell
git -c "http.https://github.com.proxy=" clone <地址> <目标目录>
```

**`CONNECT tunnel failed, response 502` / `Recv failure: Operation timed out`**
GitHub 的 git 通道不可用。换 Gitee，或用上面的压缩包降级方案（HTTPS 下载通常还能走）。

**`destination path already exists`**
目录已存在。`git -C <目标目录> pull` 更新；不是 git 仓库就先移走旧目录。

**装完 Agent 没识别到**
确认装在客户端实际读取的目录下，且 `SKILL.md` 在目录第一层（不是套了一层子目录）。

---

## 许可证

MIT License
