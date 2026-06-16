# craft-web

让 web 前端工作看起来像真人维护者写的，而不是 AI 默认生成的。Mavis skill。

> English version: [README.md](./README.md)

## 这个 skill 干什么

`craft-web` 给模型一套**约束**（什么**不**该做），用于任何 web 前端任务——新建、修复、重构、优化。**不**规定固定的页面结构或回复形状。

约束分三层：

| 层 | 覆盖 |
|---|---|
| **Voice（沟通）** | 不要开场白、不要无意义道歉、不要"让我想想"的开场白、用户报 bug 时默认 bug 存在、不要揣测用户。 |
| **Visual（视觉）** | 不要紫色渐变、不要玻璃拟态、UI 里不要 emoji、配色/字体/间距克制、用真实的字体系统。 |
| **Code（代码）** | 业务语义命名、不留 `console.log` 痕迹、数据视图三态（loading/empty/error）、真正做无障碍、不泄露密钥。 |

## 什么时候会触发

模型在用户问任何涉及 web 前端代码、样式、UX 的请求时加载。典型例子：

- "给我做个 SaaS landing page"
- "修一下这个 UI bug——按钮点了没反应"
- "优化一下界面，AI 味太重了"
- "数据库连不上"
- "帮我修个网页"
- "加点动效" / "改改样式"

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

## 设计原理

**约束 ≠ 模板。** AI 写网页最常见的失败模式是"模板匹配"——看到 "SaaS landing" 就自动吐出 header / hero / logo-bar / feature / workflow / pricing / cta / footer 八段式。`craft-web` 明令禁止。模型应该读懂真实需求，再决定。

**Pre-ship checklist，而不是事后复盘。** 基线 LLM 经常是写完代码再列自己的问题。`craft-web` 把 8 项 checklist（XSS / 密钥 / 无障碍 / 三态 / 响应式 / 视觉克制 / 清理 / 命名）放在写代码**之前**，让交付物本身就干净。

**verbosity 跟任务走。** 一行 bug fix 不该配 5 段解释；整页重写才值得更多上下文。skill 明说不要为了"看起来 thorough"而制造仪式。

## 怎么评测

验证一次修改到底有没有让 skill 进步（而不是倒退），跑一轮 producer-vs-baseline 对比：

1. 选一个真实的用户 prompt，应该能触发这个 skill。
2. 并行起两个 agent：
   - **Producer**：加载 `SKILL.md`，回答 prompt。
   - **Baseline**：同样 prompt，但**不**加载 skill。
3. 对比这几项：
   - skill 是不是产出了更有结构/更完整的结果？
   - 是不是避免了不必要的仪式或模板套用？
   - producer 的推理过程里能看到 pre-ship checklist 吗？
   - 对比 baseline 有没有 regression（长度、聚焦、质量）？

停止条件：producer 明显胜过 baseline，或连续两轮无明显改进。

## 视觉校准锚点

模型需要视觉参考时，看这些：

- `linear.app`
- `vercel.com`
- `stripe.com/docs`
- `github.com`
- `rauchg.com`

不要看 Dribbble。不要看"AI 生成的 landing page 灵感板"。

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