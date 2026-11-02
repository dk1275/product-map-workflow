# product-map-workflow

**让 AI 干活前先看地图，不瞎翻代码。**

一个任务执行 Skill：AI 每次动手前先读产品地图和源码索引，定位到具体文件再改，
改完把新情况写回去。省 token、少改错、能交接。

---

## 快速安装

### macOS / Linux

```bash
mkdir -p ~/.workbuddy/skills && git clone https://gitee.com/dk1275/product-map-workflow.git ~/.workbuddy/skills/product-map-workflow
```

### Windows（PowerShell）

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.workbuddy\skills" | Out-Null; git clone https://gitee.com/dk1275/product-map-workflow.git "$env:USERPROFILE\.workbuddy\skills\product-map-workflow"
```

装给 Codex 就把 `.workbuddy` 换成 `.codex`。

---

### 源一：Gitee（推荐，国内直连）

```
https://gitee.com/dk1275/product-map-workflow.git
```

不想敲命令，把下面这段直接丢给你的 AI：

> 帮我安装 product-map-workflow 这个 Skill。
>
> 源：`https://gitee.com/dk1275/product-map-workflow.git`
> 目标：`~/.workbuddy/skills/product-map-workflow`
> （Windows 用 `%USERPROFILE%\.workbuddy\skills\product-map-workflow`；
> 我用的是 Codex 就把 `.workbuddy` 换成 `.codex`）
>
> 用 git clone 装，父目录不存在先建。装完必须验证：目标目录下有 `SKILL.md`，
> 且文件开头是 `---` 包起来的 frontmatter，里面有 `name: product-map-workflow`。
> 验证不过就告诉我真实报错，不要说装好了。
> 最后告诉我：实际路径、用的哪个源、`git log -1 --oneline` 的提交号。

### 源二：GitHub

```
https://github.com/dk1275/product-map-workflow.git
```

把上面那段提示词里的地址换成这个，并加上一句：

> 如果 `github.com` 连不上（报 502 或超时），告诉我，别自己重试超过两次。

GitHub 的 git 通道在部分网络下连不通。真连不上，直接用 Gitee，内容一致。

---

## 这个 Skill 到底干嘛的

一句话：**它让 AI 知道"这个项目里，哪段代码在哪个文件"**。

AI 没有持久记忆。每次开新会话，它面对你的项目就是一张白纸 —— 不知道订单逻辑在
`order.js` 还是 `checkout.js`，不知道谁在调那个函数，不知道上次为什么绕开了某个写法。

没地图的时候，它只有两条路：**全读**（把几十个文件塞进上下文）或者**瞎猜**（改错地方）。

这个 Skill 就是逼它先读一张地图，地图里有三样东西：

| 内容 | 作用 |
|---|---|
| **功能节点** | 这个产品有哪些能力，各自归哪块 |
| **源码索引** | 每个源文件负责什么、什么症状该读它、关键函数叫什么、有没有验过 |
| **历史坑点** | 踩过的坑，别再踩第二次 |

然后配一条硬规矩：**单次最多读 3 个文件，每个都得说清为什么读**。

---

## 为什么很有必要装

因为 **AI 的默认行为就是浪费**。

不给约束，它遇到"购物车总价算错了"会这么干：

```
让我先看看项目结构 → 读 12 个页面文件 → 读 utils 全部 → 读配置
→ 上下文塞满 → 真正能用来思考的空间没了 → 最后改了个不痛不痒的地方
```

有地图之后：

```
购物车总价错 → 查索引「总价」→ 命中 cart.js:calcTotal
→ 读 cart.js + utils/format.js（2 个文件）→ 改 → 完事
```

同样的任务，token 差几倍到几十倍，准确率还更高。

而且现在一个项目经常被**多个 AI 轮流改** —— Codex 改一版、Cursor 改一版、
WorkBuddy 再改一版。没有统一的地图和交接记录，第三个来的 AI 就得从头摸一遍，
还会把前两个已经想明白的事重新想错一次。

---

## 目标

**用最少的 token，做最准的改动，并且让下一个接手的人或 AI 不用重新摸索。**

具体拆成四条：

1. **不瞎读** —— 定位到文件再读，单次最多 3 个
2. **不瞎改** —— 动手前知道谁依赖谁，改一个地方不会连带弄坏三处
3. **不重复踩坑** —— 坑点写在文件里，不散落在聊天记录里
4. **能交接** —— 新会话、新 AI、换人，读一遍地图就能接着干

---

## 它解决什么问题

| 你遇到的问题 | 没有它 | 有它 |
|---|---|---|
| AI 每次从头探索项目 | 每个新会话重新 grep、重新读文件 | 读索引一行就知道改哪个文件 |
| 上下文被无关代码塞满 | 一次读几十个文件，真正能思考的空间不够 | 单次最多 3 个，有理由才读 |
| 改一个地方坏了三个 | 不知道谁在调这个函数 | 索引写着被依赖，动手前就知道爆炸半径 |
| 踩过的坑重复踩 | 上次怎么绕开的，这次全忘了 | 坑点沉淀在 `历史坑点`，自动生效 |
| 规则靠你每次重复交代 | 你得反复说"别用浏览器模拟操作""别吞异常" | 写进 `约束黑名单` / `禁用写法`，是硬约束 |
| 换 AI、换人接不上 | 前面判断全丢，重新来一遍 | 地图 + 交接记录，读一遍就接上 |
| 文档和代码对不上 | README 写得挺好，代码早改完了 | 索引和代码同任务更新，过期即失效 |

---

## 价值在哪

**省钱。** 这是最直接的。一个中等项目，全量读一遍轻松几万 token；按索引定位读 3 个文件，
常常不到两千。日常改 bug 的场景，token 差距是数量级的。

**少错。** 大部分"AI 改坏了别的功能"，根因不是模型笨，是它不知道谁依赖谁。
索引里「关键符号 + 被依赖 + 触发场景」这三列，就是给改动划边界。

**可沉淀。** 用完的会话会消失，但地图会留下。今天想明白的架构判断、今天踩的坑，
明天换个 AI 还在。这对长期维护的项目是复利。

**不跑偏。** `约束黑名单` 和 `禁用写法` 是硬约束 —— 比如"不用浏览器模拟人工操作"
"不写空 catch""不在索引里写密钥"。这些不用你每次重复说。

---

## 它会产出什么

给项目建一份 `product-map.md`，固定这些章节：

`核心目标` · `业务清单` · `功能节点` · `源码索引` · `约束黑名单` · `禁用写法` ·
`复用组件` · `历史坑点` · `输出格式` · `Token-节约规则`

其中 `源码索引` 是强制项，长这样：

| 路径 | 职责 | 触发场景 | 关键符号 | 状态 |
|---|---|---|---|---|
| `utils/request.js` | 统一请求封装、token 携带、错误提示 | 接口报错 / 401 / 超时 | `request()`、拦截器 | 已验证 |
| `pages/cart/cart.js` | 购物车渲染、选中、数量、总价 | 购物车数据不对 / 总价算错 | `onSelect`、`calcTotal` | 已实现 |

`触发场景` 这一列是省 token 的关键：**没有匹配的触发，这个文件就不读**。

规则细节、防缺陷清单、交付契约都在 [`SKILL.md`](./SKILL.md)。

---

## 更新

```bash
git -C ~/.workbuddy/skills/product-map-workflow pull
```

```powershell
git -C "$env:USERPROFILE\.workbuddy\skills\product-map-workflow" pull
```

## 装完怎么确认

```bash
head -3 ~/.workbuddy/skills/product-map-workflow/SKILL.md
```

应该看到 `---` 开头，里面 `name: product-map-workflow`。

## 连不上怎么办

**`Failed to connect to 127.0.0.1 port 7890`**
git 配了本地代理但代理没开。清掉再试：

```bash
git -c "http.https://github.com.proxy=" clone <地址> <目标目录>
```

**GitHub 报 502 / 超时**
换 Gitee 源，或者用压缩包：

```bash
mkdir -p ~/.workbuddy/skills/product-map-workflow
curl -L https://gitee.com/dk1275/product-map-workflow/repository/archive/main.zip -o /tmp/pmw.zip
unzip -q /tmp/pmw.zip -d /tmp/pmw
cp -R /tmp/pmw/product-map-workflow-main/. ~/.workbuddy/skills/product-map-workflow/
```

## 许可证

MIT License
