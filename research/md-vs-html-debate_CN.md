# 面向 LLM 与 AI Agent 的文档 / 标记格式 —— 一份中立的参考资料

**用途。** 本文作为一项技术写作项目、以及一份可能的文档格式规范（GEML）的参考素材。它记录了当前关于「哪种标记/文档格式最适合 LLM 与 AI Agent」的持续争论——这场争论在一定程度上由 Anthropic 的 Claude Code 博客文章 *"The unreasonable effectiveness of HTML"* 引发。本文刻意保持中立：批评意见与反驳都被如实记录下来，**包括那些同样适用于一种新格式的批评**。

**下文使用的阅读约定**
- **【有来源】**（[SOURCED]）= 归属于某个具体来源的论断（参见第 6 节的 Sources 列表）。
- **【综合推断】**（[SYNTHESIS]）= 我自己的串联性分析，不能归于单一来源。
- 当某个来源自身的措辞是关键论据时，我会带出处逐字引用。
- 抓取说明：主文章及大多数反应文章均可访问并已直接抓取。凡我依赖的是二手摘要而非原文之处，我会在行内注明。

---

## 1. 源文章的核心论点

**文章：** "Using Claude Code: The unreasonable effectiveness of HTML"，作者 Thariq Shihipar（Anthropic 的 Claude Code 团队成员）。发布于 Claude/Anthropic 博客；传播甚广（据报道浏览量 >4M）。已直接抓取。**【有来源】**

### 1.1 核心论断
该论证**并非**在抽象意义上主张 HTML 是比 Markdown 更好的*格式*。它主张的是：**对于人类真正需要参与、审阅的 agent 输出而言**，一旦文档变大，Markdown 作为一个*人类参与界面*就失效了，而 HTML 让人类得以重新保持「在环」（in the loop）。两篇反应文章（Roger Wong；explainx 的摘要）各自独立地强调了这一区分：作者「并不是在论证 markdown 作为一种格式更差——他论证的是它作为一个*人类参与工具*失效了」。**【有来源】**

作者自己陈述的真实动机，引自 Roger Wong 的评述：**"The real reason I use HTML is that I feel much more in the loop with Claude."** **【有来源】**

### 1.2 它援引的具体机制
出自文章（已抓取）及佐证性摘要：**【有来源】**

1. **规模化后可读性崩溃。** *"I tend to not actually read more than a 100-line Markdown file"*——超过这个规模，审阅就退化为走过场式的橡皮图章。HTML 的视觉结构（标签页、页内导航、作为版式的标题）让长文档保持可导航。
2. **表达力差距。** Markdown 逼迫人们采用粗糙的变通手段——"ASCII diagrams"，或"estimating colors with unicode characters"。HTML 原生承载**表格**、**用 CSS 样式化的设计数据**、**SVG 插图**，以及**通过 HTML+JS+CSS 实现的交互**。
3. **可分享性。** Markdown 文件"are fairly hard to share since most browsers do not render them natively well"。一个自包含的 HTML 文件可以在任何地方打开，可以通过邮件发送或托管，且不需要任何特定的 Markdown 渲染器。
4. **双向交互。** HTML 可以暴露"sliders or knobs to adjust a design"，外加导出按钮，让用户"copy these changes into a prompt to paste back"——把产物变成一个输入设备，而不只是一个输出。
5. **综合汇聚界面。** 借助 Claude Code 的文件系统访问 + MCP，HTML 是一个自然的场所，可以把跨来源的信息*融合*进一份内容丰富的文档。

### 1.3 它给出的具体示例 **【有来源】**
- **设计原型**，带有"several sliders and options … to try different options on this animation"。
- **代码审阅产物**："render the actual diff with inline margin annotations, color-code findings by severity"。
- **定制编辑器**：用于工单优先级排序的"draggable card across Now / Next / Later / Cut columns"。
- **报告**："a diagram of the token-bucket flow, the 3–4 key code snippets annotated"。

### 1.4 文章自身承认的限定条件 **【有来源】**
- 它**承认 Markdown "often uses fewer tokens"**，但主张在 ~1M-token 的上下文窗口下，增加的表达力带来了"overall better output"。
- 作者给自己贴的标签是**"probably far on the HTML maximalist side of things"**，主动标明了自己的偏向。

> **【综合推断】** 这一论点比标题更窄、也更站得住脚。它真正的意思是：*"对于那些从视觉结构与交互性中获益的、面向人类的交付物，就请求 HTML；接受 token 成本，因为现在上下文很便宜，而人类的理解力才是瓶颈。"* 大量的反驳（第 4 节）针对的是那个「极端主义式」的标题，而非这个更窄的论断——这个落差值得留意，它对 GEML 自身的定位也有意义。

---

## 2. 争论地图 —— 焦点 / 维度

以下是人们真正争论的各个维度。每一条都在多个来源中反复出现。**【对各【有来源】维度的综合推断】**

1. **Token 成本 / 经济性。** 表示同样内容需要多少 token——影响推理成本、延迟，以及留给推理的上下文预算还剩多少。
2. **源文件（而非仅渲染输出）的人类可读性。** 人能否读懂并信任原始产物，还是只能看渲染后的视图？
3. **可审阅性 / 可审计性。** 人能否有意义地审阅并签字确认？diff 是否干净？"If it can't be reviewed, it's a toy."
4. **人机共同创作 / 往返（round-tripping）。** 人能否直接编辑产物，还是必须重新 prompt 模型？版本控制的 diff 是否清晰易读？
5. **结构精确性与可寻址性。** 该格式是否暴露出稳定、具名的结构，使 agent 能够精确定位（读取/修补某个特定部分），而不必吞入整个文件？
6. **解析确定性 / 歧义。** 同一份源文件是否总是以相同方式被解析，还是会因方言与上下文而异？
7. **模型*产出*它的可靠程度。** 该格式在训练数据中是否有充分表示，使模型能正确、一致地生成它？
8. **渲染保真度与表达力。** 它能否承载表格、图示、交互性、精确的版式？
9. **安全性。** 产物是否仅仅因为被打开就变成了可执行代码（JS → XSS / 数据外泄）？
10. **生态 / 工具链 / 可移植性。** 它能否在 GitHub、Notion、Slack、浏览器、Pandoc 中"just render"？还是需要定制的托管/工具？
11. **模型性能敏感性（依赖任务与模型）。** 格式选择会可度量地改变准确率——但*最佳*格式因模型和任务而异。
12. **内容的可验证性（文档所断言之事的正确性）。** 这与格式本身有别——文档中的论断/引用能否被机器校验？（这一维度在主流争论中大体*缺席*，正是 GEML 立下新根基之处——参见第 5 节。）

---

## 3. 各格式的优 / 缺点（**按讨论所提出的方式**，附归属）

> 除非标记为**【综合推断】**，否则所有条目均**【有来源】**、归属于相关讨论。数字因来源/tokenizer/内容而异，最好读作*方向性*的，而非权威定论。

### 3.1 Markdown

**优点（按讨论）**
- **Token 高效。** 被反复引用为最大的优势。所报告的数字跨度很大，取决于内容以及拿它与什么样的 HTML 相比：~68% 更少的 token（AgentMail/Tarik Davis 的说法）；在一个内容完全相同的示例上减少 67%（web2md）；Cloudflare 的 ~16,180 HTML → 3,150 Markdown token（~80%）；Sanity 的 ~100K → ~3,300（~97%），针对一个大量含标记/CSS 的页面。**【有来源】** *（那些极端的百分比来自剥离含 CSS/JS 的 HTML，而非纯文本对纯文本。）* **【综合推断】**
- **给推理留出更多上下文预算。** 更低的开销 → 在处理 RAG/大文档任务时，"meaningful accuracy improvements when ingesting Markdown over raw HTML"。**【有来源 —— AgentMail】**
- **在某些一对一较量中任务准确率更好。** 一项厂商基准（web2md，GPT-4 tokenizer + 3 个模型）报告 Markdown 输入相较 HTML 的收益：摘要 +31%、问答 +23%、要点抽取 +40%、改写 +39%、翻译 +8%。**【有来源 —— 作为厂商基准对待，单一来源。】**
- **对强模型往往是最优的。** arXiv 研究 "Does Prompt Formatting Have Any Impact on LLM Performance?"（2411.10541）发现**对 GPT-4 而言 Markdown 往往是最优的**。**【有来源】**
- **干净的 diff、可直接人工编辑、版本控制友好。** "Editable in any text editor with clean version-control diffs."**【有来源 —— AgentMail；Kurtis Redux】**
- **通用语 / 到处都能渲染。** "Paste it into GitHub, GitLab, Notion, or Slack and it renders natively."**【有来源 —— Kurtis Redux】**
- **默认安全。** 纯文本不携带可执行载荷。**【有来源】**
- **人在环内的共同创作。** 人与模型可以编辑*同一份源文件*；做一处小改动无需重新 prompt 往返（"when I already have a clear idea of what I want to say … that's just another roadblock" —— HN 用户 *tmhrtly*）。**【有来源 —— HN】**

**缺点（按讨论）**
- **表达力天花板。** 没有原生的图示、交互性、精确版式或样式化数据 → 粗糙的变通手段（ASCII 艺术、unicode "颜色"）。**【有来源 —— 源文章】**
- **超过约 100 行后可读性崩溃**（至少对部分用户如此）→ 流于表面的审阅。**【有来源 —— 源文章】**
- **碎片化为多种方言；解析有歧义。** "Easy but fragmented into dialects." *同一份* Markdown 在不同解析器中解析结果不同（强调规则、引用消解）。**【有来源 —— dasroot/Slant；djot 文档】**
- **对结构是有损的。** 当*结构本身*重要时（例如分析一个网页），"HTML keeps the structural details that Markdown throws away"。**【有来源 —— Tarik Davis】**
- **在浏览器中无法原生渲染**，必须经过一道步骤。**【有来源 —— 源文章】**

### 3.2 HTML

**优点（按讨论）**
- **表达力最强。** 表格、SVG、CSS、内嵌交互性，皆在一份产物中。**【有来源 —— 源文章】**
- **保留 Markdown 丢弃的结构/语义**（尤其针对页面结构分析）。**【有来源 —— Tarik Davis】**
- **只要有浏览器就能到处渲染；作为单个 `index.html` 自包含且可分享。** **【有来源 —— 源文章；HN 用户 momojo/l3x4ur1n】**
- **模型能很好地产出它**——在训练数据中表示极其充分。**【有来源 —— HN】**
- **把输出变成一个输入界面**（滑块、导出到 prompt）。**【有来源 —— 源文章】**
- **在大型交付物上让人类保持"in the loop"**，靠的是视觉导航。**【有来源 —— 源文章；Roger Wong】**
- **适合交付物**：规范、报告、仪表盘、审阅 UI。**【有来源 —— HN 共识】**

**缺点（按讨论）**
- **吞 token 且生成更慢。** "2–3× more tokens for clean content and 8–10× with CSS and JavaScript, and … 2–4× longer to generate."**【有来源 —— AgentMail】**
- **源文件对人眼很不友好；只有渲染后才可读。** "HTML is only readable after rendering; its raw source is inherently hostile to human eyes."**【有来源 —— Kurtis Redux】**
- **可审计性退化。** "If humans only consume rendered output, the ability to audit what the agent actually wrote degrades … anything that can only be reviewed after rendering is structurally weaker than something legible in its source."**【有来源 —— AgentMail】** 更直白地说：**"If it can't be reviewed, it's a toy."**【有来源 —— Kurtis Redux】**
- **安全边界。** "Agent-generated JavaScript becomes runnable code in the reader's browser … reading text becomes running code." 有人援引 Google 的 A2UI 协议，认为它之所以存在，*正是因为*企业安全团队不会接受 agent 写出任意可运行的 HTML。**【有来源 —— AgentMail；searchcans 摘要】**
- **嘈杂的 diff；编辑需要工具。** 用重新 prompt 代替手工编辑；版本控制的可读性差。**【有来源 —— Kurtis Redux；HN】**
- **千篇一律的疲劳感。** Claude 生成的 HTML "all looks identical"（"ugh another one" —— HN 用户 *fuglede_*）。**【有来源 —— HN】**
- **注意力稀释（属声称）。** Redux 论证冗长的标记"dilutes the model's attention … increasing hallucination risk"。**【有来源 —— Kurtis Redux；注：属断言，该文并未做基准测试。】** **【综合推断】**
- **利益冲突的观感。** 多位评论者指出，一位 Anthropic 内部人士推广一种*更吞 token* 的模式"raises conflict-of-interest flags"。**【有来源 —— Kurtis Redux；HN】**

### 3.3 Markdown + 内嵌 HTML / MDX（「混合」的折中路线）
这是 HN 上获赞最多的建设性立场。**【有来源 —— HN】**
- **优点：** 让正文保持在可读/可 diff 的 Markdown 中；仅在需要交互性或富表格的地方切入 HTML/JSX 组件（HN *jedimastert*；MDX 被称为"the perfect middle ground"）。有人构建了一道轻量的处理步骤："simpler text in markdown and rich visuals and complex tables in html"（HN *sreekanth67*）。
- **缺点：** **【综合推断】** 它继承了两套工具链的复杂性；MDX 需要 JSX/React 构建；在内嵌 HTML/JS 之处仍然具备 XSS 能力；diff 的干净程度只能与那些内嵌的「孤岛」一样干净。

### 3.4 djot（John MacFarlane —— Pandoc 与 CommonMark 的创造者）
被定位为"Markdown's ambiguity, fixed"。**【有来源 —— djot 文档/规范；jonashietala】**
- **优点 —— 确定性。** 设计目标是"parse in linear time, with no backtracking"；行内解析是**局部的**（不依赖后面才定义的引用）。相比之下，Markdown "requires backtracking"。**【有来源】**
- **优点 —— 无歧义。** 强调规则简单且平衡（`_` 表示 emphasis，`*` 表示 strong），对比 CommonMark 的"daunting list of 17 rules"。Markdown 中非局部的引用消解"makes accurate syntax highlighting nearly impossible"。**【有来源】**
- **缺点：** **【综合推断】** 相对于 Markdown 生态极小；GitHub/Notion/Slack 不原生渲染它；模型产出它的可靠性远低（训练数据很少）——这正是 Markdown 无处不在的反面。

### 3.5 AsciiDoc
**【有来源 —— adoc-studio；dewanahmed；hyperpolyglot】**
- **优点：** 源文件可读，**外加**一整套技术文档功能集（表格、交叉引用、include）；单一源 → HTML/PDF/DocBook/manpage。语义比 Markdown 更强，又不必上升到 HTML。
- **缺点：** **【综合推断】** 语法更重；生态更小；模型产出它的可靠性低于 Markdown；工具链（Asciidoctor）是一项依赖。

### 3.6 reStructuredText（reST）
**【有来源 —— dewanahmed；hyperpolyglot；Slant】**
- **优点：** 丰富、精确的 directive/role 体系；与 Sphinx 深度集成 → 自动交叉引用、索引、API 文档；对大型软件文档很强。
- **缺点：** **【综合推断】** 空白/directive 语法挑剔且易出错（对人和模型皆然）；绑定于 Python 生态；冗长。

### 3.7 Typst
**【有来源 —— HN 提及；泛指】** 注：我能检索到的专门针对 Typst-vs-LLM 的分析很稀薄；视作来源较弱。
- **优点（按讨论所提出）：** "Beautifully formatted documents"，可编程，可用 Pandoc 转换，支持 Mermaid——一个面向高保真输出的现代 LaTeX 替代品。
- **缺点：** **【综合推断】** 面向排版打印输出，而非 agent 往返；有编译步骤；训练数据占比很小。

### 3.8 org-mode
**【有来源 —— HN 用户 *jaaron*；hyperpolyglot】**
- **优点：** "Significantly more powerful system"——行内任务、可执行代码块（babel）、literate 配置、可用 Pandoc 转换。
- **缺点：** **【综合推断】** 实践中与 Emacs 深度耦合；离开 Emacs 就很小众；模型产出它不一致。

### 3.9 JSON / YAML / XML（结构化，用于 prompt 与机器交换）
**【有来源 —— arXiv 2411.10541；Felix Pappe】**
- **优点：** 无歧义的机器结构；当*消费方是另一个程序/agent* 时最理想；类 XML 的标签能为模型干净地界定各段。arXiv 研究发现**JSON 对 GPT-3.5 表现更好**；一些实践者偏好用 XML 标签做强段落界定。
- **缺点：** **【有来源 + 综合推断】** 吞 token（引号、花括号、缩进）；对人类阅读正文的体验差；YAML 的空白很脆弱；arXiv 研究表明*最佳*结构化格式是**依赖模型与任务的**，因此不存在普适赢家。GPT-3.5 在一项代码任务上因格式不同而变动**高达 40%**；GPT-4 对格式选择**更鲁棒**。**【有来源】**

---

## 4. 最尖锐的反驳与引言（附归属）

**支持 HTML / 重新考虑 Markdown**
- **Simon Willison**（有保留的背书，*仅限输出*）：*"I've been defaulting to asking for most things in Markdown since the GPT-4 days, when the 8,192 token limit meant that Markdown's token-efficiency over HTML was extremely worthwhile."* → *"Thariq's piece here has caused me to reconsider that, especially for output."* → *"Asking Claude for an explanation in HTML means it can drop in SVG diagrams, interactive widgets, in-page navigation …"* **【有来源 —— simonwillison.net】** 注：他的转变明确是关于**输出**，而非输入，且带有探索性质。
- **源文章**（诚实的核心）：*"The real reason I use HTML is that I feel much more in the loop with Claude."* **【有来源 —— 经由 Roger Wong】**

**反对 HTML / 支持源文件可读性**
- **Kurtis Redux**（主要的反驳文，"The Unreasonable Ineffectiveness of HTML"）：
  - *"HTML is only readable after rendering; its raw source is inherently hostile to human eyes."*
  - *"Running unvetted, AI-generated JS risks XSS or local data leaks. Reading text has now become running code."*
  - *"If it can't be reviewed, it's a toy."*
  - 关于激励：让一位内部人士*"encourage more token-hungry usage patterns raises conflict-of-interest flags"*。**【有来源】**
- **AgentMail**（把可审计性作为第一性原理）：*"If reviewability is what makes an artifact serious, anything that can only be reviewed after rendering is structurally weaker than something legible in its source."* **【有来源】**
- **HN —— *tmhrtly***（共同创作的摩擦）：为做一处编辑而重新 prompt，*"when I already have a clear idea of what I want to say in my head, that's just another roadblock"*。**【有来源】**
- **HN —— *fuglede_***（千篇一律）：所有 Claude 生成的 HTML 看起来都一样——"ugh another one"。**【有来源】**

**双方达成一致的综合结论**
- HN 共识（在该帖摘要中的转述）：**HTML 擅长*交付物*（规范、报告、仪表盘）；Markdown 更适合*协作*与*迭代*。** 用"unreasonably"（不合常理地）一词，反映的是对「模型能处理如此大量的标记而不降级」的惊讶——而非说 HTML 有多新颖。**【有来源 —— HN】**
- 生命周期法则（AgentMail）：**"format should follow intended artifact lifespan and surface, not be standardized universally."** **【有来源】**

**标准泛滥的警告（适用于任何*新*格式，包括 GEML）**
- **XKCD 927（"Standards"）**：试图统一 14 个相互竞争的标准，结果得到 15 个。这是对"让我们引入一种新格式"的经典反驳。此处援引，是因为它将是 GEML 遇到的*第一个*反对意见。**【有来源 —— xkcd.com/927】**

---

## 5. 对 GEML 的启示与开放问题

**一句话讲清 GEML（依据 brief）：** 一种纯文本文档格式，其中*一切*都是一个带类型、可用 `#id` 寻址的块，具备构建期引用校验（`geml check`）、自包含的版本历史（`.gemlhistory`），以及按 id 对单个块进行定向读取/修补（`geml get/set #id`），从而使 agent 无需吞入整个文件。

### 5.1 GEML 在争论各维度上的位置——以及它能很好地解决哪些痛点 **【综合推断，以【有来源】维度为根基】**

| 维度（出自 §2） | GEML 可能的处境 |
|---|---|
| Token 成本 | **强**，前提是源文件是纯文本且像 Markdown 一样简洁（而非像 HTML 那样冗长）。可块寻址的读取/修补才是更大的杠杆：agent 只取/修补*一个块*而非整个文件——这与 Anthropic 自己的"just-in-time retrieval"（按需检索）上下文工程指引、以及业界转向选择性、按需上下文的方向直接契合。**【有来源 —— Anthropic 上下文工程；Sourcegraph】** |
| 源文件可读性 | **设计上就好**（纯文本）——绕开了反 HTML 阵营最核心的抱怨（"only readable after rendering"）。**【有来源类比】** |
| 可审阅性 / 干净的 diff | **强**——纯文本 diff 很干净；内置的 `.gemlhistory` 是对"if it can't be reviewed it's a toy"的一个*格式原生*的回答。**【有来源类比 —— Kurtis Redux/AgentMail】** |
| 人机共同创作 | **好**，前提是人能在任意编辑器里手工编辑块（即 *tmhrtly* 所说的"无需重新 prompt 往返"的优势）。需留意：`geml set #id` 绝不能让手工编辑*感觉*成了强制项。**【有来源 —— HN】** |
| 结构精确性与可寻址性 | **这是 GEML 的招牌优势。** 带类型的 `#id` 块恰好提供了讨论中所说的、稳定且具名的抓手——这是 Markdown 缺失、而 HTML 只能偶然提供的（SPA "lose link-addressability unless deliberately architected" —— HN *apsurd*）。这与编程 agent 领域趋同的结论相呼应：**精确字符串 / 块锚定的编辑**（`str_replace_editor`、`apply_patch`）比按行号或整文件编辑*更可靠*。**【有来源 —— HN；dev.to 文件编辑基准；AG2】** |
| 解析确定性 | **机会所在**——GEML 可以采纳 djot 的原则：局部、无回溯、无歧义的解析。在这一点上给出一份硬性规范，是相对于 Markdown 的一项真正优势。**【有来源 —— djot】** |
| *内容*的可验证性（引用） | **GEML 的差异化赌注。** `geml check`（构建期引用校验）瞄准的是一个主流 MD-vs-HTML 争论几乎不触及的维度。它在精神上更接近 reST/Sphinx 的交叉引用消解、以及编译器式的检查，而不是「更漂亮的 Markdown」。要以此为卖点主打，而非表达力。**【综合推断；reST/Sphinx 类比有来源】** |
| 渲染保真度 / 交互性 | **最弱，而这没关系。** GEML *不应*试图在 HTML 的地盘上比 HTML 更 HTML。若需要富视觉/交互性，诚实的打法是把*交付物*编译成 HTML，同时保留 GEML 作为*可审阅、可编辑、可校验的源*——这与 HN 的"交付物 vs 源"之分相吻合。**【有来源 —— HN】** |
| 安全性 | **强**，前提是 GEML 保持声明式/不可执行（源中没有任意 JS）。它保住了"纯文本是安全的"这一属性；一旦它编译成交互式 HTML，XSS 面就会在那个边界上重新出现。**【有来源 —— AgentMail】** |
| 生态 / "到处都能渲染" | **结构性地最弱。** 这是最难、也最重要的批评（§5.2）。 |

### 5.2 本争论中 GEML 必须诚实回应的批评 **【综合推断，每一条都以某个【有来源】反对意见为根基】**

1. **XKCD 927 / "为什么又要一种新格式？"** 最先冒出的反应就是：GEML 是第 15 个标准。**需要诚实回答：** 指名道姓地说出 Markdown/HTML/AsciiDoc 在结构上无法服务的那个*具体*维度——证据指向的是 **`#id` 可寻址性 + 构建期引用校验**，也就是*可验证、可被 agent 修补的结构*，而非漂亮。如果卖点漂移向"更好用的 Markdown"，那 927 就赢了。**【有来源 —— xkcd 927】**

2. **生态 / 通用语问题（致命一击）。** Markdown 的决定性优势在于它能在 GitHub、GitLab、Notion、Slack、浏览器、Pandoc 中"renders natively"。而一种新格式在第一天*哪里都不能*渲染。**需要诚实回答：** 提供无摩擦的 `geml → Markdown/HTML` 导出，最好还有 GitHub/预览渲染，让 GEML 成为唯一事实来源（source-of-truth），而不强迫其他所有人的工具做改变。每一种小众格式（djot、AsciiDoc、org、Typst）都卡在这里。**【有来源 —— Kurtis Redux；格式对比】**

3. **"模型产出它不可靠。"** HTML/Markdown 之所以赢，部分是因为它们在训练数据中占满了席位。一种全新的语法则 ~ 零。**需要诚实回答：** 让语法保持最小且规整（djot 式的确定性对模型也有帮助）；考虑 GEML 是否与 Markdown 足够接近，使得 few-shot / 上下文中一份简短规范就足以让模型可靠地产出；并准备好展示参考解析器会拒绝格式错误的输出（快速反馈闭环）。**【有来源 —— HN "models emit HTML well"；djot】**

4. **共同创作的摩擦。** 如果编辑一个块在现实中需要 `geml set #id` 工具、而不是打开文件直接敲字，那 GEML 就重新制造了当初针对 HTML 的那个"another roadblock"（又一个绊脚石）的抱怨。**需要诚实回答：** 在纯文本编辑器中手工编辑必须保持一等公民地位；`get/set` 是一种*agent 优化*，而非唯一路径。**【有来源 —— HN tmhrtly】**

5. **类型与 id 脚手架的冗长 / token 成本。** 带类型的块 + 显式的 `#id` 会增加字符。如果一份 GEML 文档明显比等价的 Markdown 更重，它就部分放弃了 Markdown 的招牌优势。**需要诚实回答：** 去度量它；让块头保持简洁；并倚重*净* token 论证——修补一个已寻址的块胜过重新发出整个文件，因此即便文件略大，整套工作流的 token 用量也可能下降。**【有来源 —— token 成本讨论；Anthropic 按需检索】**

6. **"注意力稀释 / 更多结构 ≠ 更好的输出。"** Redux 声称冗长的标记会提高幻觉风险。**需要诚实回答：** arXiv 研究是双刃剑——格式效应确实存在，但**依赖模型与任务**，且强模型更鲁棒。不要过度声称 GEML *改善推理*；只声称它改善*可寻址性、可审阅性与可验证性*。**【有来源 —— 2411.10541；Kurtis Redux】**

7. **利益冲突 / 对炒作的怀疑。** 那篇 HTML 文章招来了"你从更多 token 中获益"的犬儒质疑。一种由其自身倡导者撰写的格式，将面对镜像般的质疑。**需要诚实回答：** 公开参考解析器、规范，以及可复现的 token/往返基准；让 `geml check` 展示一项能力，而非一种感觉。**【有来源 —— Kurtis Redux/HN】**

### 5.3 值得纳入 GEML 规范的、来自讨论的想法 **【对【有来源】想法的综合推断】**
- **明确采纳 djot 的解析纪律**：线性时间、无回溯、*局部*行内解析、平衡/无歧义的定界符。把确定性作为一项明示目标写进规范。这对人类、工具链、*以及*模型产出都好。**【有来源 —— djot】**
- **定位 = "唯一事实来源，编译为交付物。"** 拥抱 HN 的"交付物 vs 协作源"之分：GEML 是可审阅/可校验/可修补的**源**；HTML（或 MD）是一个**生成的视图**。不要在渲染上竞争。**【有来源 —— HN】**
- **把 `geml check` 做成招牌功能，框定为「面向文档的编译器」。** 整场 MD-vs-HTML 之争都忽视了*内容可验证性*。构建期引用校验是一个真实、可演示、且新颖的维度——最接近的先例是 reST/Sphinx 的交叉引用，但作为一项*一等的、独立的*保证，它是独树一帜的。**【有来源 —— reST/Sphinx 类比；综合推断】**
- **顺势用好按需 / 可寻址的检索。** Anthropic 自己的上下文工程指引与业界方向都青睐让 agent 拉取*它所需的那个特定块*。`geml get #id` 正是为此而生的格式原生原语——把它作为战略顺风来引用。**【有来源 —— Anthropic 上下文工程；Sourcegraph；LangChain】**
- **对齐那种已经奏效的 agent 编辑粒度。** 编程 agent 趋同于用**精确字符串 / 块锚定**的编辑，而非按行号或整文件的 diff，因为前者更可靠。以 `#id` 为范围的 `set` 就是文档层面的对应物——把它规范好，使一次修补能确定性地只命中一个块。**【有来源 —— dev.to 文件编辑基准；AG2 apply_patch】**
- **让源保持不可执行。** 保住"纯文本是安全的"这一属性；若/当 GEML 编译为交互式 HTML 时，将其视作一个明示的、需主动选择进入的信任边界。**【有来源 —— AgentMail 安全论点】**
- **尽早交付导出 + 渲染。** 把"能在 GitHub 渲染 / 预览干净 / 可往返到 Markdown"当作 P0 级的规范/工具要求，因为对挑战者格式而言，被生态拒之门外是经验上决定成败的失败模式。**【有来源 —— 格式对比；Kurtis Redux】**

### 5.4 开放问题（讨论未解决；GEML 必须自行决定） **【综合推断】**
- GEML 是否足够简洁，使其相对于 Markdown 的每文件 token 开销可忽略——你能否*拿出证据*表明块修补带来的净 token 优势？
- 模型能否从一份简短规范 + few-shot 就产出有效的 GEML，还是需要微调才能可靠？（这决定了采用的上限。）
- `#id` 可寻址性能否在真实的人工编辑中存活（id 不失效、不被改名、不重复）？`geml check` 对悬空/重复的 id 会怎么处理？
- "GEML 源"与"编译后的 HTML 交付物"之间的边界在哪里，交互性/安全性的责任由谁在那个接缝处承担？
- 诚实的范围究竟是"结构化、可验证、对 agent 友好的**正文/规范**文档"，而非"通用的 Markdown 替代品"？楔子越窄，就越能抵御 927。

---

## 6. Sources

主文章
- Thariq Shihipar / Anthropic — *Using Claude Code: The unreasonable effectiveness of HTML* — https://claude.com/blog/using-claude-code-the-unreasonable-effectiveness-of-html （已抓取）

反应 / 讨论
- Simon Willison — *The Unreasonable Effectiveness of HTML* — https://simonwillison.net/2026/May/8/unreasonable-effectiveness-of-html/ （已抓取）
- Kurtis Redux — *The Unreasonable Ineffectiveness of HTML* — https://kurtis-redux.medium.com/the-unreasonable-ineffectiveness-of-html-5bd01ae1e879 （已抓取）
- Roger Wong — *What Humans Actually Read* — https://rogerwong.me/2026/05/what-humans-actually-read （已抓取）
- Hacker News 讨论帖 — https://news.ycombinator.com/item?id=48071940 （经由摘要抓取）
- explainx.ai 摘要 — https://explainx.ai/blog/unreasonable-effectiveness-html-claude-code-thariq-2026 （经由搜索摘要）
- Pasquale Pillitteri — *HTML vs Markdown in Claude Code* — https://pasqualepillitteri.it/en/news/2243/html-vs-markdown-claude-code-thariq-anthropic （经由搜索摘要）
- claudeai.dev — *Claude Code and the Unreasonable Effectiveness of HTML Artifacts* — https://claudeai.dev/blog/claude-code-html-artifacts/ （经由搜索摘要）

格式对比（Markdown vs HTML，面向 LLM/agent）
- AgentMail — *HTML vs Markdown for AI agents* — https://www.agentmail.to/blog/html-vs-markdown-for-ai-agents （已抓取）
- Tarik Davis — *Markdown vs HTML for LLM Agents: The 2026 Format Showdown* — https://www.tarikdavis.co.uk/blog/markdown-vs-html-for-llm-agents-the-2026-format-showdown/ （已抓取）
- web2md — *HTML vs Markdown for LLMs: I Wasted 67% of My Tokens* — https://web2md.org/blog/markdown-vs-html-for-llm （已抓取）
- searchcans — *Markdown vs. HTML for LLM Context* — https://www.searchcans.com/blog/markdown-vs-html-llm-context-optimization-2026/ （经由搜索摘要）
- searchcans — *Why Markdown is the Preferred LLM Output Format in 2026* — https://www.searchcans.com/blog/markdown-llm-output-benefits/ （经由搜索摘要）
- releasepad — *HTML vs. Markdown: The Optimal Format for LLM Content Ingestion* — https://www.releasepad.io/blog/html-vs-markdown-the-optimal-format-for-llm-content-ingestion/ （经由搜索摘要）
- beam.ai — *HTML vs Markdown for AI Agents* — https://beam.ai/agentic-insights/html-vs-markdown-which-format-actually-makes-ai-agents-more-useful （经由搜索摘要）
- Digiday — *WTF is Markdown for AI agents?* — https://digiday.com/media/wtf-is-markdown-for-ai-agents/ （经由搜索摘要）

替代格式
- djot — 仓库与理据 — https://github.com/jgm/djot ；*Why Djot?* — https://php-collective.github.io/djot-php/guide/why-djot ；Jonas Hietala — *Blogging in Djot instead of Markdown* — https://www.jonashietala.se/blog/2024/02/02/blogging_in_djot_instead_of_markdown/ （经由搜索摘要）
- CommonMark — https://commonmark.org/ （经由搜索摘要）
- adoc-studio — *AsciiDoc vs Markdown, LaTeX & reStructuredText (2026)* — https://www.adoc-studio.app/blog/why-asciidoc （经由搜索摘要）
- Dewan Ahmed — *Markdown, Asciidoc, or reStructuredText* — https://www.dewanahmed.com/markdown-asciidoc-restructuredtext/ （经由搜索摘要）
- Hyperpolyglot — *Lightweight Markup* — https://hyperpolyglot.org/lightweight-markup （经由搜索摘要）
- dasroot — *Markdown vs AsciiDoc vs reStructuredText* — https://dasroot.net/posts/2026/03/markdown-vs-asciidoc-vs-restructuredtext-choosing-right-markup-language/ （经由搜索摘要）
- Felix Pappe — *Structured Prompting for LLMs: YAML, JSON, XML or Plain Text?* — https://felix-pappe.medium.com/structured-prompting-for-llms-from-raw-text-to-xml-daf39b461f13 （经由搜索摘要）

研究
- Jia He et al. — *Does Prompt Formatting Have Any Impact on LLM Performance?* — arXiv 2411.10541 — https://arxiv.org/abs/2411.10541 （经由搜索摘要）

Agent 文件编辑与上下文工程
- dev.to (ceaksan) — *I Benchmarked 5 File Editing Strategies for AI Coding Agents* — https://dev.to/ceaksan/i-benchmarked-5-file-editing-strategies-for-ai-coding-agents-heres-what-actually-works-1855 （经由搜索摘要）
- AG2 — *GPT-5.1 Apply Patch Tool* — https://docs.ag2.ai/latest/docs/blog/2025/12/22/GPT-5.1-Apply-Patch-Tool/ （经由搜索摘要）
- Fabian Hertwig — *Code Surgery: How AI Assistants Make Precise Edits* — https://fabianhertwig.com/blog/coding-assistants-file-edits/ （经由搜索摘要）
- Anthropic — *Effective context engineering for AI agents* — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents （经由搜索摘要）
- Sourcegraph — *Context Engineering: A Practical Guide for AI Agents (2026)* — https://sourcegraph.com/blog/context-engineering （经由搜索摘要）
- LangChain — *Context engineering in agents* — https://docs.langchain.com/oss/python/langchain/context-engineering （经由搜索摘要）

文化引用
- XKCD 927 — *Standards* — https://xkcd.com/927/ ；explain xkcd — https://www.explainxkcd.com/wiki/index.php/927:_Standards

**来源可靠性说明。** 若干格式对比类博客属于厂商/营销内容（AgentMail、web2md、beam.ai、searchcans、releasepad），它们对某一答案有既得利益；其*数字*（token 百分比、任务提升百分比）来自单一的、往往未经复现的基准，应作为方向性证据看待。最独立的证据点是：类同行评审的 arXiv 研究（2411.10541）、djot 的设计文档（John MacFarlane），以及主来源文章 + 具名个人（Simon Willison、Kurtis Redux、Roger Wong）。
