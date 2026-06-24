# OpenMontage：AI 视频这件事，可能不能只盯着“生成”看了

> 平台：微信公众号 | 字数：约3300字 | 调性：技术观察、偏个人判断

---

这两天翻 GitHub Trending 和 AI Agent 圈子，很容易撞见 OpenMontage。

它的自我介绍很猛：开源的 agentic video production system。翻成人话，就是想把 Claude Code、Cursor、Copilot、Windsurf、Codex 这类 AI coding assistant，改造成一个能做视频的“制作助理”。

但我真正感兴趣的不是这句 slogan。

现在一说 AI 视频，大家第一反应还是文生视频：输入一句 prompt，模型给你吐一段画面。这个方向当然重要，也够吸睛。但如果你真的做过一点视频，就知道麻烦不在“有没有一段画面”。

麻烦在前后那一长串活儿。

选题要查资料。脚本要有结构。镜头要能接起来。素材要找得到。旁白、音乐、字幕、转场、比例、节奏，哪个掉链子都很明显。最后还要看一遍成片，确认不是字幕错位、音量爆掉、画面像 PPT。

OpenMontage 有意思的地方正在这里：它没有把视频当成一次生成，而是当成一套生产流程。

这个视角，比“又一个 AI 视频工具”更值得聊。

## 先说它是什么

OpenMontage 是 calesthio 开源在 GitHub 上的项目。我查的时候是 2026-06-24，仓库主语言是 Python，许可证是 AGPL-3.0，约 17.6k stars、2k forks，当天还有 push。

也就是说，它至少不是一个没人管的周末 demo。

项目自己的说法是：把 AI coding assistant 变成完整的视频制作工作室。听起来有点夸张。拆开看，倒是能明白它为什么这么说。

仓库里有几类关键目录：

- `pipeline_defs/`：不同视频类型的流程定义；
- `skills/`：给 Agent 读的执行说明；
- `tools/`：真正干活的 Python 工具；
- `schemas/`：用来检查产物结构；
- `remotion-composer/`：用 Remotion 做程序化视频合成。

这个结构透露出它的底层思路：视频不是直接从一个大模型里“冒出来”的。Agent 要先读流程，读技能文件，再调用工具，一步步把东西做出来。

更直白点，它把很多过去藏在人脑里的制作经验，写成了 Agent 能读的操作手册。

这件事比单纯接一个视频模型更复杂，也更笨重。但笨重有时候是好事。因为真实生产本来就不轻。

## 它不只是在动几张图

很多 AI 视频工作流其实是这样：生成几张图，加镜头推拉，加字幕和音乐，再用转场串起来。效果可以挺好，尤其是做短科普、概念片、产品 teaser 的时候。

但它终究还是“图片动起来”。

OpenMontage 在 README 里刻意区分了两条路：image-based video 和 real-footage video。前者是常见的图像动画路线。后者更有意思：Agent 可以从 Archive.org、NASA、Wikimedia Commons、Pexels、Unsplash 这类素材源里找真实动态素材，建立可检索的素材库，再剪出一个由真实镜头组成的视频。

这差别不小。

纪录片蒙太奇、城市氛围片、资料影像混剪、品牌短片，这些东西不是几张漂亮静帧就能撑起来的。真实镜头里有运动、噪声、光线、场景切换，还有那种说不清但一眼能感觉到的“视频质感”。

我更愿意看这一层：它不是只追求做出一段“会动的画面”，而是让 Agent 去碰素材、剪辑和合成这些更靠近后期制作的活儿。

这和很多文生视频产品已经不是同一个路数了。

## Pipeline 是核心，不是装饰

OpenMontage 现在提供多条 pipeline。README 里列到的包括 Animated Explainer、Animation、Avatar Spokesperson、Cinematic、Clip Factory、Documentary Montage、Hybrid、Localization & Dub、Podcast Repurpose、Screen Demo、Talking Head、Character Animation 等。

名字很多，先不用被这些名词吓住。它们背后走的是同一条流程：

`research -> proposal -> script -> scene_plan -> assets -> edit -> compose`

这个流程一点也不性感。

但它像制作视频。

先研究，再提案。先写脚本，再拆分镜。先确认素材路线，再进入编辑和合成。最后还有质量检查，比如 ffprobe、抽帧、音频分析、字幕检查。

这比“一句话出片”慢，也麻烦。可如果你关心的是稳定产出，而不是抽盲盒，这种麻烦就有价值。

我甚至觉得 OpenMontage 的重点不在于它当下能做出多惊艳的视频，而在于它把“制作视频”这件事拆成了 Agent 可以跟着走的步骤。

以前我们期待模型更聪明。现在这个项目给出的答案更土：把流程写清楚，让 Agent 少乱来。

## Skills 才是这里的真主角

OpenMontage 很适合放在最近的 Agent Skill 热潮里看。

在这个项目里，skill 不是“会某个技能”的泛泛说法，而是一堆 Markdown 指令文件。Agent 进入某个阶段前，要读对应的 director skill。里面会写清楚：这一步该做什么，该用哪些工具，质量线在哪里，什么时候要让人确认。

这让我想到一个变化：过去我们把业务逻辑写进代码。现在有些“做事的方法”，开始被写成给 Agent 看的文本。

代码提供工具和状态。Agent 读规则，做判断，调用工具，检查产物。

这套东西有点别扭，但也有好处。你可以打开 skill 文件看它到底怎么要求 Agent 做视频，而不是只能相信一个黑箱。你也可以改这些文件，调整风格、预算、审批点、质量标准。

比如你不想让 Agent 乱烧 API 费用，可以写清楚付费调用前必须估算成本、必须得到确认。你不想它把纪录片做成 PPT，也可以把交付要求写进检查规则里。

这不是万能药。Agent 还是会误读，工具还是会失败，流程还是会卡。但至少问题从“模型脑子里发生了什么”变成了“哪条规则没写清楚，哪个工具不靠谱”。

这个转变很实际。

## 它接的是一整套工具，不是一个模型

OpenMontage 的工具覆盖面挺广。

视频生成有 Kling、Runway、Google Veo、MiniMax、HeyGen、WAN、Hunyuan、CogVideo、LTX-Video 等路线。图片生成有 FLUX、Imagen、DALL-E 3、Recraft、本地 Stable Diffusion，也能接 Pexels、Pixabay、Unsplash 这类素材源。TTS 包括 ElevenLabs、Google TTS、OpenAI TTS、Piper。后期则绕不开 FFmpeg、Remotion、HyperFrames、WhisperX。

看起来像报菜名。其实它说明了一件事：成熟的 AI 视频系统大概率不会只绑定一个模型。

有时你需要便宜的本地 TTS。有时你需要商业配音。做数据解释，Remotion 很合适；做动效海报，HTML/GSAP 可能更自然。真实素材能解决的问题，没必要硬上视频生成。预算很紧的时候，本地和开源工具就很重要。

OpenMontage 里还有 provider selection 的思路，会从任务匹配、质量、可控性、可靠性、成本、延迟、连续性等维度做选择。这个部分如果做好，会比“默认调用某个最贵模型”更像真实制作。

视频生产里，最强工具未必是最合适的工具。

这句话听着普通，但很多 AI 产品还没真正做到。

## 为什么它会火

我不觉得 OpenMontage 火起来只是因为它会做视频。

更大的原因可能有三个。

第一，AI coding assistant 已经不只是在补代码了。Claude Code、Codex、Cursor、Copilot 这类工具，正在变成能读文件、跑命令、改项目、执行多步任务的工作代理。OpenMontage 刚好把视频制作包装成一个它们能接手的工程项目。

第二，Agent Skill 正在变成一种新资产。以前复用的是代码库，现在复用的是“让 Agent 怎么做事”的方法。OpenMontage 的 pipeline、skill、schema、review 规则，都是这种资产。

第三，创作者开始不满足于抽卡式生成了。单次生成很酷，但持续做内容的人会问更无聊的问题：成本多少？风格能不能保持？素材从哪来？字幕准不准？失败了能不能继续？成片能不能过一遍检查？

这些问题都不炫，但每天都会遇到。

OpenMontage 试图回答的正是这些问题。

## 别把它当成一键 SaaS

这里也要泼一点冷水。

OpenMontage 目前更像开发者工具，不是普通用户打开网页就能用的产品。它需要 Python、Node.js、FFmpeg。你要理解 API key、本地依赖、Remotion、环境变量。遇到 Windows 设置、浏览器渲染、模型权限、素材下载这些问题，也得自己排。

从最近的 issue 和 PR 看，社区还在补 CI、贡献指南、多语言 README、Windows 文档、教程、依赖修复、MCP 支持。这很正常。一个突然被很多人围观的开源项目，前面往往是想象力跑得快，后面是工程细节在追。

所以，如果你想要的是“注册账号，点一下，稳定交付商业级成片”，它现在可能不是那个东西。

但如果你关心 Agent 怎么接管复杂创作流程，它很值得看。哪怕不直接用，也能学到不少设计思路。

## 我最在意的是这个：视频生产开始像软件工程了

OpenMontage 最打动我的地方，不是某个 demo 成片，也不是它列了多少 provider。

是它把视频生产拆得很像软件工程。

选题像输入。Pipeline 像流程。Skill 像操作手册。Tool 是能力边界。Schema 是校验。Checkpoint 负责恢复。Render 是构建产物。Self-review 像测试。Cost log 是预算记录。

这样说有点硬，但它确实把创作流程里的很多隐性经验，变成了可读、可改、可检查的项目文件。

这可能会是很多 Agent 应用接下来的方向。

不是让 AI 在聊天框里“帮我做一下”，而是先把一类工作工程化，再让 Agent 在这个框架里做事。工作流越清楚，Agent 越不容易乱飞；约束越具体，自动化越接近可用。

当然，这条路不会轻松。流程会显得繁琐，调试会很烦，Agent 也会犯一些很低级的错。

但我越来越觉得，真正有用的 AI 应用，最后可能都要走过这段不太酷的工程化过程。

OpenMontage 不是 AI 视频的终点。它更像一个提醒：别只盯着模型生成了什么，也要看谁能把生成、检索、剪辑、检查和成本控制串成一条能跑的线。

这条线跑稳了，AI 视频才不只是好看的 demo。

---

资料来源：GitHub 仓库 [calesthio/OpenMontage](https://github.com/calesthio/OpenMontage)、项目 README、AGENT_GUIDE、公开仓库元数据与 issue/PR 动态。仓库数据查询时间：2026-06-24。

---

## 备选标题

1. OpenMontage 火了：AI 视频终于不只是文生视频了 — 策略：反常识
2. 17k stars 背后，OpenMontage 把视频制作变成 Agent 流水线 — 策略：数据冲击
3. 这个开源项目，正在重写 AI 视频生产方式 — 策略：好奇心缺口
4. 从 prompt 到 pipeline：OpenMontage 为什么值得关注 — 策略：技术洞察
5. AI Agent 的下一个战场，可能是视频制作 — 策略：趋势判断

## 配图指导

### 封面图

- 推荐比例：公众号封面 2.35:1
- 视觉风格：深色工作台 + 多窗口视频生产界面 + 中央 Agent 流程图，科技感但不要赛博过度
- 生成 prompt：一个现代视频制作工作台，中央是一条从 research、script、scene plan、assets、edit 到 compose 的流程线，周围有视频时间线、字幕轨道、音频波形、素材库缩略图和代码窗口，画面中央有 OpenMontage 字样的项目卡片，深色背景，蓝绿色与白色高亮，真实 UI 质感，干净、专业，适合技术公众号封面，宽幅构图，左侧留出标题空间

### 正文配图

#### 配图1（位置：第「先说它是什么」后）

- 作用：解释项目架构
- 生成 prompt：一张清晰的技术架构信息图，展示 OpenMontage 的五层结构：pipeline_defs、skills、tools、schemas、remotion-composer，每层用不同颜色模块表示，箭头从 AI Agent 指向各模块，整体扁平化科技风，白底，适合公众号正文插图

#### 配图2（位置：第「Pipeline 是核心」后）

- 作用：辅助说明 pipeline 思路
- 生成 prompt：一个视频生产流水线示意图，从 research 到 proposal、script、scene_plan、assets、edit、compose，每个节点配一个小图标，如放大镜、文档、分镜板、素材库、剪刀、渲染按钮，现代扁平插画风，清晰易读

#### 配图3（位置：第「我最在意的是这个」后）

- 作用：强化核心观点
- 生成 prompt：把视频制作比喻成软件工程的概念插画，左侧是视频剪辑时间线，右侧是代码编辑器和测试面板，中间由 Agent 连接，出现 checkpoint、schema、self-review、cost log 等小标签，专业技术媒体风格，冷静但有未来感
