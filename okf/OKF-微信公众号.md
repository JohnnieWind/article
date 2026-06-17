# Google 发布 OKF：AI Agent 终于需要一个“知识文件夹标准”了

> 平台：微信公众号 | 字数：约2200字 | 调性：理性深度、技术科普

---

过去一年，很多团队都在做同一件事：给 AI Agent 喂上下文。

一开始，大家把文档丢给 RAG，把代码仓库接进 IDE，把数据库 schema、指标口径、运维手册、API 文档、项目约定分散塞进各种工具里。结果很快出现一个新问题：模型越来越强，但它真正需要的“组织知识”仍然到处散落。

一个 Agent 想回答“周活用户应该怎么计算”，可能要同时读数据表结构、埋点说明、指标口径、历史变更记录、业务例外规则，甚至还要问一个老员工。问题不是模型不会推理，而是它不知道该信哪份上下文。

Google Cloud 最新发布的 Open Knowledge Format，简称 OKF，正是冲着这个问题来的。

它听起来像一个新产品，但更准确地说，它是一个很小、很朴素、也很有野心的开放规格：用一组 Markdown 文件，加上一点 YAML frontmatter，把组织知识整理成既能被人读、也能被 Agent 读的标准格式。

这件事重要的地方，不在于 Google 又做了一个工具，而在于它试图回答一个正在变得越来越关键的问题：

当 AI Agent 开始进入真实工作流，组织的知识应该以什么格式存在？

## OKF 到底是什么？

OKF 是 Google Cloud Data Cloud 团队在 2026 年 6 月 13 日正式介绍的开放规格。当前版本是 OKF v0.1，仍处在 Draft 阶段。

它的核心设计非常简单：

一个 OKF bundle，就是一个目录。

目录里是一组 Markdown 文件。

每个 Markdown 文件代表一个 concept，也就是一个知识单元。这个知识单元可以是一张数据表、一个数据集、一个指标、一份 runbook、一个 API、一条业务规则，或者任何值得被独立描述的对象。

文件顶部用 YAML frontmatter 放少量结构化字段，正文继续使用普通 Markdown。

例如，一个订单表可以写成这样：

```markdown
---
type: BigQuery Table
title: Orders
description: One row per completed customer order.
resource: https://console.cloud.google.com/bigquery?p=acme&d=sales&t=orders
tags: [sales, revenue]
timestamp: 2026-05-28T14:30:00Z
---

# Schema

| Column | Type | Description |
| --- | --- | --- |
| `order_id` | STRING | Globally unique order identifier. |
| `customer_id` | STRING | FK to customers. |

# Joins

Joined with customers on `customer_id`.
```

OKF v0.1 真正强制要求的字段只有一个：`type`。其他字段，如 `title`、`description`、`resource`、`tags`、`timestamp`，都是推荐字段。

这就是 OKF 的气质：它不是一个厚重的知识图谱标准，也不是一个必须安装 SDK 才能用的平台。它更像是给 AI 时代的组织知识定了一个最小公共格式。

普通人可以打开它，Agent 也可以解析它。它可以放在 GitHub，也可以打包成 zip；可以由人工维护，也可以由自动化脚本或 LLM 生成。

一句话概括：OKF 把“组织知识”变成了一组可读、可审查、可版本管理、可迁移的文件。

## 它要解决的不是“搜索”，而是“上下文组装”

很多人看到 OKF，第一反应可能是：这不就是 RAG 的另一种形式吗？

其实不是。

RAG 解决的是“从一堆资料里检索相关片段，再塞给模型”的问题。OKF 更靠前一步，它解决的是“这些资料应该如何被整理、命名、连接、维护，才能被不同 Agent 和工具复用”的问题。

今天企业内部的知识通常分散在很多地方：

数据目录里有表结构，Wiki 里有业务解释，代码注释里有实现细节，Notion 里有流程文档，共享盘里有历史方案，少数资深工程师脑子里还有一半没写出来的隐性知识。

这对人类已经很麻烦，对 Agent 更麻烦。

每做一个新的 Agent，都要重新接一遍数据源，重新写一套解析逻辑，重新处理指标定义和业务例外。每个供应商也都有自己的目录系统、API 和知识图谱格式。知识一旦被某个系统生产出来，就很难自然流向另一个系统。

OKF 的思路是：不要先做一个更大的平台，先把文件格式定下来。

如果知识能被导出为同一种目录结构，同一种 Markdown + YAML 约定，那么生产者和消费者就可以分离：

数据目录可以生产 OKF，Obsidian 可以浏览 OKF，搜索引擎可以索引 OKF，Agent 可以把 OKF 加载进上下文，另一个可视化工具也可以把 OKF 画成图。

格式一旦稳定，工具就可以自由竞争。

## 为什么偏偏是 Markdown？

OKF 最有意思的地方，是它没有发明复杂的新语言。

它选择了最没有神秘感的一套东西：Markdown、文件夹、YAML frontmatter、普通链接。

这看似保守，实际很聪明。

Markdown 的优势不只是“轻量”，而是它同时站在人和机器之间。人类可以直接读，工程师可以用 Git review，LLM 可以直接吞进上下文，静态站点工具可以渲染，搜索工具可以索引。

YAML frontmatter 则负责少量必须结构化的信息，比如这个知识单元是什么类型、标题是什么、对应的真实资源在哪里、有哪些标签、最后更新时间是什么。

正文部分继续保持自由。你可以写 schema 表格，可以写 SQL 示例，可以写故障处理步骤，可以写注意事项和历史背景。

更重要的是，OKF 允许概念之间用普通 Markdown 链接互相引用。目录结构提供层级，链接提供关系。一个指标可以链接到它依赖的数据表，一个 runbook 可以链接到相关 dashboard，一个 API 文档可以链接到背后的业务流程。

这样，OKF bundle 既是树，也是图。

## 这不是 Google Cloud 的私有格式

Google 在这次发布里反复强调，OKF 是 format，不是 platform。

这句话很关键。

如果 OKF 只是 Google Cloud Knowledge Catalog 的导入导出格式，那它的意义会小很多。但从规格设计看，它刻意保持了供应商中立：不绑定云厂商，不绑定数据库，不绑定模型，不要求中心注册表，也不要求专有 SDK。

当前 GitHub 仓库里，Google 同时提供了几个参考实现：

一个 enrichment agent，可以遍历 BigQuery 数据集，为表和视图生成 OKF 概念文档，再通过第二轮 LLM 抓取权威文档，补充引用、schema 和 join path。

一个静态 HTML visualizer，可以把任意 OKF bundle 渲染成可交互的图视图，而且是单文件、无后端、无安装。

还有三个示例 bundle：GA4 e-commerce、Stack Overflow 公共数据集、Bitcoin 公共数据集。

这些工具本身不是重点。重点是，OKF 想让“知识生产”和“知识消费”脱钩。

今天由 BigQuery 生成，明天可以被别的 Agent 读取；今天由人写，明天可以被可视化工具展示；今天放在 Google Cloud，明天也可以放进任意 Git 仓库。

AI 时代最危险的锁定，可能不是模型锁定，而是上下文锁定。

OKF 正是在试图降低这种锁定。

## 对团队真正有用的地方

如果你是做数据、工程、AI 应用或内部工具的，OKF 最现实的价值有三个。

第一，它把隐性上下文显性化。

很多组织不是没有知识，而是知识没有被写成 Agent 能稳定消费的形态。一个指标为什么这么算，一张表为什么不能直接 join，某个字段为什么废弃，这些信息如果只散在 Slack、飞书、Jira 和人的脑子里，Agent 很难可靠工作。

OKF 给这些知识一个统一落点。

第二，它让知识进入工程流程。

一旦知识变成文件，就可以进 Git。可以 review，可以 diff，可以 blame，可以在 PR 里讨论。知识更新不再是“谁有空去改一下 Wiki”，而可以变成软件工程的一部分。

这对 AI Agent 尤其重要。因为 Agent 的输出质量，很大程度取决于上下文质量。上下文如果不可审查、不可追踪，Agent 的错误就很难治理。

第三，它降低多 Agent 协作成本。

未来企业里很可能不是一个 Agent 包打天下，而是很多 Agent 分工协作：数据分析 Agent、客服 Agent、研发 Agent、运维 Agent、合规 Agent。

如果每个 Agent 都维护自己的知识格式，系统会很快碎片化。OKF 提供了一种共同语言：不同 Agent 可以围绕同一组知识文件工作，而不是各自重新理解世界。

## 当然，OKF 还不是终局

OKF v0.1 只是起点，甚至可以说是一个非常克制的起点。

它目前只规定最基本的结构，不定义固定的 concept type taxonomy。也就是说，`Metric`、`BigQuery Table`、`Playbook` 这些类型怎么命名，主要由生产者自己决定。

它的链接语义也很轻。一个 Markdown 链接只表示“这里有关系”，至于是依赖、引用、join、替代，还是上游下游，需要靠周围文字说明。

此外，OKF 解决不了知识质量本身的问题。错误的指标口径写成 OKF，仍然是错误的；过期的 runbook 变成 Markdown，也不会自动变新。权限、敏感信息、更新流程、引用可信度，仍然需要团队自己治理。

所以，OKF 不应该被理解成“有了它，Agent 就懂公司了”。

更准确的说法是：有了它，团队终于有机会把 Agent 需要懂的东西，放进一个更容易维护和交换的形态里。

## 你可以怎么开始？

如果一个团队想试 OKF，不必一上来改造所有知识库。

最好的起点，是选择一个高频、低范围、上下文密集的场景。

比如：核心数据指标。

先为几个最常用的数据集、表和指标建立一个小型 OKF bundle。每张表一个 Markdown，每个指标一个 Markdown。写清楚 schema、业务定义、示例 SQL、常见误用、上下游链接和引用来源。

然后把它放进 Git，用 PR 维护。

接下来，让一个内部 Agent 或搜索工具读取这个 bundle，观察它在回答问题时是否更稳定、更少幻觉、更容易引用来源。

如果效果成立，再扩展到 runbook、API、产品规则、客户支持知识。

OKF 不要求你替换现有系统。它更适合作为一个“可交换层”：从现有系统导出，经过人工和 Agent 共同补充，再被不同消费端使用。

## 结语

过去我们总说，AI Agent 的瓶颈是推理能力。

现在越来越明显的事实是：在真实组织里，Agent 的瓶颈常常是上下文。

没有上下文，再强的模型也只能猜；上下文混乱，再复杂的 Agent 框架也会把错误放大。

OKF 的发布，未必会立刻改变所有 AI 应用。但它抓住了一个正在变得核心的问题：组织知识不能永远锁在某个工具、某个 API、某个人的脑子里。

AI 时代最有价值的知识，不只是“被存储”，而是能被迁移、被审查、被链接、被引用，并且能被人和 Agent 同时读懂。

从这个意义上说，OKF 最重要的创新不是技术复杂度，而是方向感。

它提醒我们：真正可靠的 Agent，不只需要更聪明的模型，也需要更干净的知识地基。

---

## 备选标题

1. Google 发布 OKF：AI Agent 终于需要一个“知识文件夹标准”了 — 策略：好奇心缺口
2. 别只盯着模型了，Google 这次瞄准的是 Agent 的上下文问题 — 策略：反常识
3. OKF 是什么？Google 给 AI Agent 做了一套 Markdown 知识标准 — 策略：关键词科普
4. 当企业知识变成 Markdown，AI Agent 会发生什么变化？ — 策略：场景悬念
5. AI Agent 的下一道门槛：不是推理，而是组织知识格式 — 策略：观点冲击

## 配图指导

### 封面图

- 推荐比例：公众号封面 2.35:1
- 视觉风格：科技感但克制，深色背景，中心是一组 Markdown 文件夹节点，被连线组织成知识图谱；左侧留出标题文字区域。
- 生成 prompt：深色工作台背景，一个发光的文件夹结构由多个 Markdown 文档节点组成，节点之间以细线连接成知识图谱，局部可见 YAML frontmatter 和 markdown 标题符号，冷色科技感，干净克制，现代编辑器界面质感，左侧大面积留白用于标题，2.35:1 横向构图。

### 正文配图

#### 配图1（位置：“OKF 到底是什么？”之后）

- 作用：辅助说明 OKF 的文件夹结构。
- 生成 prompt：极简信息图风格，一个名为 okf bundle 的文件夹树，包含 datasets、tables、metrics、runbooks 子文件夹，每个文件夹里有 markdown 文件图标，旁边浮动 YAML frontmatter 小卡片，白底，蓝绿点缀，清晰技术文档风。

#### 配图2（位置：“它要解决的不是搜索，而是上下文组装”之后）

- 作用：强化“碎片化知识”问题。
- 生成 prompt：企业知识碎片化场景，多个孤立的信息岛分别标注 wiki、catalog、code comments、shared drive、senior engineer，中央是一个 AI agent 正在尝试连接这些信息，扁平插画，冷暖对比，清晰但不夸张。

#### 配图3（位置：“对团队真正有用的地方”之后）

- 作用：展示知识进入工程流程。
- 生成 prompt：Git pull request 界面风格的信息图，Markdown 知识文档正在被 review，旁边有 diff、comment、version history 图标，现代 SaaS 界面，浅色背景，专业简洁。

## 参考资料

- Google Cloud Blog: [How the Open Knowledge Format can improve data sharing](https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing)
- GitHub: [Open Knowledge Format v0.1 SPEC.md](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md)
- GitHub: [GoogleCloudPlatform/knowledge-catalog OKF directory](https://github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf)

