# craft-web

让 web 前端工作看起来像真人维护者写的，而不是 AI 默认生成的。UI skill。

> English version: [README.md](./README.md)

## 这个 skill 干什么

`craft-web` 给 web 前端任务（新建、修复、重构、优化）一套**约束 + 建设性视觉配方**。**不**规定固定的页面结构或回复形状。

skill 由两半组成：

- **约束**——什么**不**该做。经典的 AI 默认：紫色渐变、玻璃拟态、UI 里堆 emoji、过大的 hero、三栏等宽 feature card。
- **视觉配方**——"好看"具体长什么样。具体到 paper 色、type scale、color 配方、间距系统、密度档位。补上 v1 缺失的：告诉模型**该做什么**，而不是只说**别做什么**。

两者加起来覆盖三层：

| 层 | 覆盖 |
|---|---|
| **Visual（视觉）** | 不要紫色渐变、不要玻璃拟态、UI 里不要 emoji。**加上**建设性配方：cream paper、真实字体系统、克制的配色/间距、4-base scale、focus ring。 |
| **Voice（沟通）** | 不要开场白、不要无意义道歉、不要"让我想想"的开场白、用户报 bug 时默认 bug 存在。先结论后理由。 |
| **Code（代码）** | 业务语义命名、不留 `console.log` 痕迹、数据视图三态（loading/empty/error）、真正做无障碍、不泄露密钥。 |

## 什么时候会触发

模型在用户问任何涉及 web 前端代码、样式、UX 的请求时加载。典型例子：

- "给我做个 SaaS landing page"
- "修一下这个 UI bug——按钮点了没反应"
- "优化一下界面，AI 味太重了"
- "帮我做个登录页"
- "加点动效" / "改改样式"
- "数据库连不上"（当展示数据的页面本身有问题时）

## 什么时候**不**触发

- 纯算法题 / 系统设计题
- 没有 UI 的纯后端脚本
- CLI 工具、数据处理流水线
- 研究 / 写作任务
- 桌面应用、原生移动端、游戏开发

## 文件结构

```
/workspace/craft-web/                   ← 本目录（人类看的文档）
├── README.md                           ← 英文版（给国际读者）
└── README.zh-CN.md                     ← 中文版（本文件）

/workspace/.skills/craft-web/           ← skill 包（给 LLM 读）
└── SKILL.md                            ← 唯一文件，syncer 会自动同步到 OSS
```

注意：skill 包里**故意**只有 `SKILL.md`。没有 `README.md` / `CHANGELOG.md` / `install.sh` / `.env`。加了会污染模型 context，也会让 syncer 出问题。

## 视觉配方（v2 的核心）

v1 的问题：按它做的页面"技术上看不差"——没紫色渐变、没 emoji、没玻璃拟态——但**视觉上平庸**，像是占位符，永远不会有人想回去打磨一遍。

根因很简单：**禁制清单只告诉你别做什么，没告诉你该做什么**。模型读到"不要紫色渐变、不要 emoji、用真实字体系统"之后，默认走最无聊的诠释：白底 + Inter + 三个 feature card + 灰字。

v2 把比例倒过来。**视觉配方成了主角**——具体的 paper 色、明确的 type scale、命名好的 color 配方、密度档位。约束清单退居二线当安全网。

### 1. 背景和表面

纯 `#FFFFFF` 读起来像没做完。挑一个 paper 色，带一点点暖或冷。

| 模式 | Paper | Ink | 适用 |
|---|---|---|---|
| 亮色暖 | `#FAFAF7`（warm cream）或 `#F8F8F5` | `#0A0A0A` | SaaS、文档、营销——默认 |
| 亮色冷 | `#F7F8FA` | `#0B0D12` | dashboard、dev tools |
| 暗色 | `#0A0A0A`（**不**用 `#000`） | `#EDEDEA` | 暗色模式默认——永远不要纯黑 |
| 暗色 premium | `#0E0F12` | `#E8E8E5` | 高级感（Linear dark、Vercel） |

边框：亮色用 `rgba(0,0,0,0.08)`，暗色用 `rgba(255,255,255,0.08)`。永远不要 solid 1px 灰。

### 2. 字体系统

最多两张脸（sans + 可选 mono）。用真正的字重，不要靠 CSS 假装加粗。

Sans 候选（挑一个家族，通篇用它）：
- **Inter / Geist / Inter Tight** —— SaaS、dashboard 默认
- **IBM Plex Sans** —— dev tools、技术向
- **Söhne / Switzer** —— premium（付费字体）
- **system-ui** —— 零资源预算时

Mono 候选（UI 里出现代码时）：
- **JetBrains Mono / Geist Mono / IBM Plex Mono**

Type scale（除非有强理由偏离，按这套用）：

```
display    56–72px  / 600 weight / -0.02 to -0.04em tracking / line-height 1.05–1.1
h1         40–48px  / 600 / -0.015em / 1.1–1.2
h2         28–32px  / 600 / -0.01em  / 1.2–1.3
h3         20–24px  / 500–600 / -0.005em / 1.3
body       15–16px  / 400            / 1.5–1.6
caption    13–14px  / 500            / 1.4
mono       13–14px  / 400            / 1.5
```

两种字重是基线，三种（400 / 500 / 600）也 OK。**四种就太多了**——加之前先确认是不是真的需要。

### 3. 配色配方

模式：**1 brand + 1 accent + ink + paper + 2 grays**。总共 6 个。再多就是混乱。

**Brand color**：饱和但不要 neon。AI 默认紫 → 别用。试 ink-blue（`#1E40AF`）、forest（`#166534`）、或 muted coral（`#DC2626`）。饱和度 60–80%，亮度 35–50%。

**Accent**：比 brand 低调。常是 brand 的深/浅版，或者带一点色调的中性色。

**Grays**：一个 warm（`#737373` 系），一个 cool（`#6B7280` 系）。paper-on-light 用 warm，dev tools / dashboard 用 cool。

配方示例（Linear 风）：
```
brand    #5E6AD2    （真正的 purple-blue，不是 Tailwind purple-600）
accent   #4F46E5
ink      #0A0A0A
paper    #FAFAF7
gray-w   #737373
gray-c   #6B7280
```

配方示例（Vercel 风）：
```
ink      #000000
paper    #FFFFFF
accent   #0070F3
gray     #666666
```

### 4. 间距

4-base scale，通篇一致：
```
4 / 8 / 12 / 16 / 24 / 32 / 48 / 64 / 96 / 128 / 160 / 192
```

Section 之间的纵向节奏：**96–160px**。Section 内部：组与组 **24–48px**，兄弟元素 **8–16px**。Hero 到第一个内容块：**64–96px**。

紧凑度是设计选择，不是默认。Linear 紧凑，Vercel 留白多。**选一个就通篇用它**——混着密度看起来不一致。

### 5. 密度

三档，按场景选：

- **紧凑**（Linear、dashboard、数据表格）：行高 32–36px，内边距 8–12px，字体 13–14px。用户要扫很多行时用。
- **中等**（Notion、应用主界面）：行 40–48px，内边距 12–16px，字体 15px。**大部分 app 的默认**。
- **宽松**（Vercel docs、营销 hero、博客）：内边距 24–48px，字体 16–18px，行高宽。阅读型界面用。

### 6. 动效

- ≤ 300ms。默认 150–200ms。
- 进入用 ease-out，退出用 ease-in。ease-in-out 只用于状态翻转。
- 没有 bounce。没有 parallax。没有 scroll-jacking。
- Focus ring 是动效的一部分：`:focus-visible` 时可见，2px 偏移，brand 色 40% 透明。

### 7. 图标系统

一套、一个 stroke width、通篇用：
- **Lucide**（默认，1.5px stroke）—— 大多数场景
- **Tabler**（2px stroke）—— 更密的图标
- **Phosphor**（regular weight）—— 需要更多视觉存在感时

不要混。不要用 emoji 替代图标。

### "像真人做的"组件细节

这些是真维护者会做的、AI 默认会漏的：

- **真正的 focus ring**（不只是 `outline: none`）
- **Hover state 会轻微移动**（1px translate、bg 微调）—— 不是只翻色
- **Active/pressed state 和 hover 分开**
- **Disabled state**：透明度降低 + `cursor: not-allowed` 两件套
- **Skeleton loader 匹配真实布局**（不是通用灰矩形）
- **Empty state**：小插画/图标 + 真实下一步 CTA，不是"No data"
- **Error state**：说清楚发生了什么 + 该怎么办，不是 stack trace
- **键盘快捷键**在 hover tooltip 里显示（⌘K 等）

## 为什么有 v2（v1 → v2 的教训）

v1 的 `craft-web` 有个问题：按它做的页面"技术上看不差"——没紫色渐变、没 emoji、没玻璃拟态——但视觉上平庸到像占位符。

根因：**约束清单只能告诉你别做什么，不能告诉你该做什么**。模型读完"不要紫色渐变、不要 emoji、用真实字体系统"后，默认走最无聊的诠释：白底 + Inter + 三个 feature card + 灰字。

v2 把比例倒过来。**视觉配方是主角**——具体 paper 色、明确 type scale、命名好的 color 配方、密度档位。约束清单退到安全网的位置。

## 视觉校准锚点——每个学什么

不要只逛这些站，要学每个**具体擅长的东西**。

- **linear.app** —— 密度控制和 typographic rhythm。紧凑数据 UI 是教科书。右栏宽度有讲究。按钮高度统一。
- **vercel.com** + **vercel.com/docs** —— 黑白配色的克制、慷慨的留白、用 size 而不是 color 做层级。hero 就是整个品牌。
- **stripe.com/docs** —— code sample 看起来像真代码（不是 `lorem ipsum` 的 code block）。侧边导航本身就是设计对象。表格排版。
- **github.com** —— functional UI，视觉克制服务于可用性。"真维护者"的 baseline：交付需要的，不要装饰。
- **rauchg.com** —— prose 优先的个人站：typography、line-length、垂直节奏。marketing-template 的反面。
- **geist-ui.dev** —— 当你需要 component library 时（Geist + Geist Mono + 上面那套间距 scale）。

不要看 Dribbble。不要看"AI 生成的 landing page 灵感板"。那些就是 AI 默认的训练数据。

## 怎么评测

验证一次修改到底有没有让 skill 进步（而不是倒退），跑一轮 producer-vs-baseline 对比：

1. 选一个真实的用户 prompt，应该能触发这个 skill。
2. 并行起两个 agent：
   - **Producer**：加载 `SKILL.md`，回答 prompt。
   - **Baseline**：同样 prompt，但**不**加载 skill。
3. 对比这几项：
   - skill 是不是产出了更有结构/更完整的结果？
   - 是不是避免了不必要的仪式或模板套用？
   - producer 的选择里能看到 Visual Recipe 吗？
   - producer 有没有抓到 baseline 没意识到的"沉默 AI 默认"（比如细微的渐变）？
   - 对比 baseline 有没有 regression（长度、聚焦、质量）？

停止条件：producer 明显胜过 baseline，或连续两轮无明显改进。

我们跑过的 v2 评测（给一个 AI 写作工具做 SaaS landing）：
- Producer 严格遵循配方（1 个 gradient、0 emoji、显式 Inter / Inter Tight / JetBrains Mono）
- Baseline 做出了看着干净的页面，但有 **5 个沉默的 gradient**，self-report 里完全没意识到

这就是配方要捕捉的那类 regression。

## 怎么更新 skill

1. 改 `/workspace/.skills/craft-web/SKILL.md`。
2. 保持 ≤ 500 行。哪一节膨胀了，挪到 `references/<topic>.md` 然后链过去。
3. 任何规则改动后，跑一轮 producer-vs-baseline 评测。
4. syncer 在会话结束时自动上传到 OSS，不需要手动 deploy。

## 修改时别踩的坑

- 在 skill 包里塞 `README.md` / `CHANGELOG.md` / `install.sh` / `.env`
- 写 ALWAYS / NEVER / MUST 规则却不解释为什么
- description 里堆关键词而不是触发短语
- 把触发列表在 body 里再复制一遍
- 给 LLM 能直接做的事写脚本

完整反模式清单见 `skill-creator/references/anti-patterns.md`。

## 这个 skill **不是**什么

- **不是 design system。** 不要因为 skill 提到就装 shadcn。
- **不是 Tailwind preset。** 配方可以移植到 vanilla CSS、CSS Modules、Vue，任何技术栈都行。
- **不是用户判断的替代品。** 用户如果有强偏好（比如"匹配我们的 brand book"），按用户的来——配方是默认，不是法律。
