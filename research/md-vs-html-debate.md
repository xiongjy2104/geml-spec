# Document / Markup Formats for LLMs & AI Agents — A Balanced Reference

**Purpose.** Reference material to inform a technical-writing project and a possible document-format spec (GEML). This captures the ongoing debate about which markup/document formats serve LLMs and AI agents best, sparked in part by Anthropic's Claude Code blog post *"The unreasonable effectiveness of HTML."* It is deliberately balanced: criticisms and counterpoints are recorded faithfully, **including ones that would also apply to a new format**.

**Reading conventions used below**
- **[SOURCED]** = a claim attributed to a specific source (see Sources list, section 6).
- **[SYNTHESIS]** = my own connective analysis, not attributable to one source.
- Where a source's own framing is load-bearing, I quote it verbatim with attribution.
- Fetch note: the primary article and most reaction pieces were reachable and fetched directly. Where I relied on a secondary summary rather than the primary text, I say so inline.

---

## 1. The source article's thesis

**Article:** "Using Claude Code: The unreasonable effectiveness of HTML," by Thariq Shihipar (on the Claude Code team at Anthropic). Published on the Claude/Anthropic blog; widely circulated (reported >4M views). Fetched directly. **[SOURCED]**

### 1.1 The core claim
The argument is **not** that HTML is a better *format* than Markdown in the abstract. It is that **for agent output that a human is meant to actually engage with**, Markdown fails as a *human-engagement surface* once documents get large, and HTML restores the human's ability to stay "in the loop." Two reaction pieces (Roger Wong; the explainx summary) independently stress this distinction: the author "doesn't argue markdown is inferior as a format — he argues it fails as a *human engagement tool*." **[SOURCED]**

The author's own stated motivation, quoted in Roger Wong's write-up: **"The real reason I use HTML is that I feel much more in the loop with Claude."** **[SOURCED]**

### 1.2 The specific mechanisms it cites
From the article (fetched) and corroborating summaries: **[SOURCED]**

1. **Readability collapse at scale.** *"I tend to not actually read more than a 100-line Markdown file"* — beyond that, review degrades into rubber-stamping. HTML's visual structure (tabs, in-page nav, headings-as-layout) keeps long documents navigable.
2. **Expressiveness gap.** Markdown forces crude workarounds — "ASCII diagrams," or "estimating colors with unicode characters." HTML natively carries **tables**, **CSS-styled design data**, **SVG illustrations**, and **interactions via HTML+JS+CSS**.
3. **Shareability.** Markdown files "are fairly hard to share since most browsers do not render them natively well." A self-contained HTML file opens anywhere, can be emailed or hosted, and needs no particular Markdown renderer.
4. **Two-way interaction.** HTML can expose "sliders or knobs to adjust a design," plus export buttons that let the user "copy these changes into a prompt to paste back" — turning the artifact into an input device, not just an output.
5. **Synthesis surface.** With Claude Code's filesystem access + MCP, HTML is a natural place to *fuse* cross-source information into one rich document.

### 1.3 Concrete examples it gives **[SOURCED]**
- **Design prototypes** with "several sliders and options … to try different options on this animation."
- **Code-review artifacts**: "render the actual diff with inline margin annotations, color-code findings by severity."
- **Custom editors**: a "draggable card across Now / Next / Later / Cut columns" for ticket prioritization.
- **Reports**: "a diagram of the token-bucket flow, the 3–4 key code snippets annotated."

### 1.4 Caveats the article itself acknowledges **[SOURCED]**
- It **concedes Markdown "often uses fewer tokens"** but argues the added expressiveness yields "overall better output" given a ~1M-token context window.
- The author self-labels as **"probably far on the HTML maximalist side of things,"** flagging his own bias.

> **[SYNTHESIS]** The thesis is narrower and more defensible than the headline. It is really: *"For human-facing deliverables that benefit from visual structure and interactivity, request HTML; accept the token cost because context is now cheap and human comprehension is the bottleneck."* Much of the pushback (section 4) engages the maximalist headline rather than this narrower claim — a gap worth keeping in mind for GEML's own positioning.

---

## 2. Map of the debate — the focal points / axes

These are the dimensions people actually argue on. Each recurs across multiple sources. **[SYNTHESIS of SOURCED axes]**

1. **Token cost / economy.** How many tokens to represent the same content — affects inference cost, latency, and how much context budget is left for reasoning.
2. **Human readability of *source* (not just rendered output).** Can a person read and trust the raw artifact, or only the rendered view?
3. **Reviewability / auditability.** Can a human meaningfully review and sign off? Are diffs clean? "If it can't be reviewed, it's a toy."
4. **Human co-authoring / round-tripping.** Can a human edit the artifact directly, or must they re-prompt the model? Are version-control diffs legible?
5. **Structural precision & addressability.** Does the format expose stable, named structure an agent can target (read/patch a specific part) rather than ingesting the whole file?
6. **Parse determinism / ambiguity.** Does the same source always parse the same way, or is it dialect- and context-dependent?
7. **How reliably the model *emits* it.** Is the format well-represented in training data so models produce it correctly and consistently?
8. **Rendering fidelity & expressiveness.** Can it carry tables, diagrams, interactivity, precise layout?
9. **Security.** Does the artifact become executable code (JS → XSS / data exfiltration) simply by being opened?
10. **Ecosystem / tooling / portability.** Does it "just render" in GitHub, Notion, Slack, browsers, Pandoc? Or need bespoke hosting/tooling?
11. **Model-performance sensitivity (task- and model-dependent).** Format choice measurably changes accuracy — but the *best* format varies by model and task.
12. **Verifiability of content (correctness of what the doc asserts).** Distinct from format — can claims/references in the doc be machine-checked? (This axis is largely *absent* from the mainstream debate and is where GEML stakes new ground — see section 5.)

---

## 3. Pros / cons per format (as raised BY the discourse, attributed)

> All bullets are **[SOURCED]** to the discourse unless marked **[SYNTHESIS]**. Numbers vary by source/tokenizer/content and are best read as *directional*, not canonical.

### 3.1 Markdown

**Pros (per discourse)**
- **Token-efficient.** Repeatedly cited as the biggest win. Reported figures span a wide range depending on content and what HTML it's compared against: ~68% fewer tokens (AgentMail/Tarik Davis framing); a 67% reduction on one identical-content example (web2md); Cloudflare's ~16,180 HTML → 3,150 Markdown tokens (~80%); Sanity's ~100K → ~3,300 (~97%) for a page heavy in markup/CSS. **[SOURCED]** *(The extreme percentages come from stripping CSS/JS-laden HTML, not from prose-vs-prose.)* **[SYNTHESIS]**
- **Leaves more context budget for reasoning.** Lower overhead → RAG/large-doc tasks see "meaningful accuracy improvements when ingesting Markdown over raw HTML." **[SOURCED — AgentMail]**
- **Better task accuracy in some head-to-heads.** One vendor benchmark (web2md, GPT-4 tokenizer + 3 models) reported Markdown-input gains over HTML: summarization +31%, Q&A +23%, key-point extraction +40%, rewriting +39%, translation +8%. **[SOURCED — treat as vendor benchmark, single source.]**
- **Often optimal for strong models.** The arXiv study "Does Prompt Formatting Have Any Impact on LLM Performance?" (2411.10541) found **Markdown was often optimal for GPT-4**. **[SOURCED]**
- **Clean diffs, direct human editing, version control.** "Editable in any text editor with clean version-control diffs." **[SOURCED — AgentMail; Kurtis Redux]**
- **Lingua franca / renders everywhere.** "Paste it into GitHub, GitLab, Notion, or Slack and it renders natively." **[SOURCED — Kurtis Redux]**
- **Safe by default.** Plain text carries no executable payload. **[SOURCED]**
- **Human-in-the-loop co-authoring.** Humans and models can edit the *same source*; no re-prompt roundtrip to make a small change ("when I already have a clear idea of what I want to say … that's just another roadblock" — HN user *tmhrtly*). **[SOURCED — HN]**

**Cons (per discourse)**
- **Expressiveness ceiling.** No native diagrams, interactivity, precise layout, or styled data → crude workarounds (ASCII art, unicode "colors"). **[SOURCED — source article]**
- **Readability collapses past ~100 lines** for at least some users → superficial review. **[SOURCED — source article]**
- **Fragmented into dialects; ambiguous parsing.** "Easy but fragmented into dialects." The *same* Markdown parses differently across parsers (emphasis rules, reference resolution). **[SOURCED — dasroot/Slant; djot docs]**
- **Lossy for structure.** When the *structure itself* matters (e.g., analyzing a web page), "HTML keeps the structural details that Markdown throws away." **[SOURCED — Tarik Davis]**
- **Doesn't render natively in browsers** without a step. **[SOURCED — source article]**

### 3.2 HTML

**Pros (per discourse)**
- **Maximal expressiveness.** Tables, SVG, CSS, embedded interactivity in one artifact. **[SOURCED — source article]**
- **Preserves structure/semantics** that Markdown discards (esp. for page-structure analysis). **[SOURCED — Tarik Davis]**
- **Renders anywhere a browser exists; self-contained & shareable** as a single `index.html`. **[SOURCED — source article; HN users momojo/l3x4ur1n]**
- **Models emit it well** — extremely well-represented in training data. **[SOURCED — HN]**
- **Turns output into an input surface** (sliders, export-to-prompt). **[SOURCED — source article]**
- **Keeps humans "in the loop"** on large deliverables via visual navigation. **[SOURCED — source article; Roger Wong]**
- **Good for deliverables**: specs, reports, dashboards, review UIs. **[SOURCED — HN consensus]**

**Cons (per discourse)**
- **Token-hungry & slower to generate.** "2–3× more tokens for clean content and 8–10× with CSS and JavaScript, and … 2–4× longer to generate." **[SOURCED — AgentMail]**
- **Source is hostile to human eyes; only readable after rendering.** "HTML is only readable after rendering; its raw source is inherently hostile to human eyes." **[SOURCED — Kurtis Redux]**
- **Auditability degrades.** "If humans only consume rendered output, the ability to audit what the agent actually wrote degrades … anything that can only be reviewed after rendering is structurally weaker than something legible in its source." **[SOURCED — AgentMail]** And bluntly: **"If it can't be reviewed, it's a toy."** **[SOURCED — Kurtis Redux]**
- **Security boundary.** "Agent-generated JavaScript becomes runnable code in the reader's browser … reading text becomes running code." Google's A2UI protocol is cited as existing *because* enterprise security teams won't accept agents writing arbitrary runnable HTML. **[SOURCED — AgentMail; searchcans summary]**
- **Noisy diffs; editing needs tools.** Re-prompting instead of hand-editing; poor version-control legibility. **[SOURCED — Kurtis Redux; HN]**
- **Sameness fatigue.** Claude-generated HTML "all looks identical" ("ugh another one" — HN user *fuglede_*). **[SOURCED — HN]**
- **Attention dilution (claimed).** Redux argues verbose markup "dilutes the model's attention … increasing hallucination risk." **[SOURCED — Kurtis Redux; note: asserted, not benchmarked in that piece.]** **[SYNTHESIS]**
- **Conflict-of-interest optics.** Multiple commenters note an Anthropic insider promoting a *more token-hungry* pattern "raises conflict-of-interest flags." **[SOURCED — Kurtis Redux; HN]**

### 3.3 Markdown + embedded HTML / MDX (the "hybrid" middle ground)
The single most-upvoted constructive HN position. **[SOURCED — HN]**
- **Pro:** Keep prose in readable/diffable Markdown; drop into HTML/JSX components only where interactivity or rich tables are needed (HN *jedimastert*; MDX called "the perfect middle ground"). Some build a lightweight step: "simpler text in markdown and rich visuals and complex tables in html" (HN *sreekanth67*).
- **Con:** **[SYNTHESIS]** Inherits both toolchains' complexity; MDX needs a JSX/React build; still XSS-capable where HTML/JS is embedded; diffs are only as clean as the embedded islands.

### 3.4 djot (John MacFarlane — creator of Pandoc & CommonMark)
Positioned as "Markdown's ambiguity, fixed." **[SOURCED — djot docs/spec; jonashietala]**
- **Pro — determinism.** Designed to "parse in linear time, with no backtracking"; inline parsing is **local** (doesn't depend on references defined later). Markdown, by contrast, "requires backtracking." **[SOURCED]**
- **Pro — no ambiguity.** Emphasis rules are simple and balanced (`_` emphasis, `*` strong) vs CommonMark's "daunting list of 17 rules." Non-local reference resolution in Markdown "makes accurate syntax highlighting nearly impossible." **[SOURCED]**
- **Con:** **[SYNTHESIS]** Tiny ecosystem vs Markdown; not natively rendered by GitHub/Notion/Slack; models emit it far less reliably (little training data) — the flip side of Markdown's ubiquity.

### 3.5 AsciiDoc
**[SOURCED — adoc-studio; dewanahmed; hyperpolyglot]**
- **Pro:** Readable source **plus** a full technical-doc feature set (tables, cross-references, includes); single source → HTML/PDF/DocBook/manpage. Stronger semantics than Markdown without going to HTML.
- **Con:** **[SYNTHESIS]** Heavier syntax; smaller ecosystem; models emit it less reliably than Markdown; toolchain (Asciidoctor) is a dependency.

### 3.6 reStructuredText (reST)
**[SOURCED — dewanahmed; hyperpolyglot; Slant]**
- **Pro:** Rich, precise directive/role system; deep Sphinx integration → auto cross-references, indexes, API docs; strong for large software docs.
- **Con:** **[SYNTHESIS]** Whitespace/directive syntax is finicky and error-prone for both humans and models; Python-ecosystem-bound; verbose.

### 3.7 Typst
**[SOURCED — HN mention; general]** Note: dedicated Typst-vs-LLM analyses were thin in what I could retrieve; treat as lighter-sourced.
- **Pro (as raised):** "Beautifully formatted documents," programmable, Pandoc-convertible, Mermaid support — a modern LaTeX alternative for high-fidelity output.
- **Con:** **[SYNTHESIS]** Aimed at typeset print output, not agent round-tripping; compile step; small training footprint.

### 3.8 org-mode
**[SOURCED — HN user *jaaron*; hyperpolyglot]**
- **Pro:** "Significantly more powerful system" — inline tasks, executable code blocks (babel), literate config, Pandoc-convertible.
- **Con:** **[SYNTHESIS]** Deeply Emacs-coupled in practice; niche outside it; models emit it inconsistently.

### 3.9 JSON / YAML / XML (structured, for prompts & machine exchange)
**[SOURCED — arXiv 2411.10541; Felix Pappe]**
- **Pro:** Unambiguous machine structure; ideal when the *consumer is another program/agent*; XML-ish tags can delimit sections cleanly for models. The arXiv study found **JSON performed better for GPT-3.5**; some practitioners favor XML tags for strong section delimitation.
- **Con:** **[SOURCED + SYNTHESIS]** Token-heavy (quotes, braces, indentation); poor human prose-reading experience; YAML whitespace is fragile; the arXiv study shows the *best* structured format is **model- and task-dependent**, so there's no universal winner. GPT-3.5 varied **up to 40%** on a code task by format; GPT-4 was **more robust** to format choice. **[SOURCED]**

---

## 4. Sharpest counterpoints & quotes (attributed)

**Pro-HTML / reconsidering Markdown**
- **Simon Willison** (qualified endorsement, *output only*): *"I've been defaulting to asking for most things in Markdown since the GPT-4 days, when the 8,192 token limit meant that Markdown's token-efficiency over HTML was extremely worthwhile."* → *"Thariq's piece here has caused me to reconsider that, especially for output."* → *"Asking Claude for an explanation in HTML means it can drop in SVG diagrams, interactive widgets, in-page navigation …"* **[SOURCED — simonwillison.net]** Note: his shift is explicitly about **output**, not input, and is exploratory.
- **Source article** (the honest core): *"The real reason I use HTML is that I feel much more in the loop with Claude."* **[SOURCED — via Roger Wong]**

**Anti-HTML / pro source-legibility**
- **Kurtis Redux** (the main rebuttal, "The Unreasonable Ineffectiveness of HTML"):
  - *"HTML is only readable after rendering; its raw source is inherently hostile to human eyes."*
  - *"Running unvetted, AI-generated JS risks XSS or local data leaks. Reading text has now become running code."*
  - *"If it can't be reviewed, it's a toy."*
  - On incentives: having an insider *"encourage more token-hungry usage patterns raises conflict-of-interest flags."* **[SOURCED]**
- **AgentMail** (auditability as a first principle): *"If reviewability is what makes an artifact serious, anything that can only be reviewed after rendering is structurally weaker than something legible in its source."* **[SOURCED]**
- **HN — *tmhrtly*** (co-authoring friction): re-prompting to make an edit *"when I already have a clear idea of what I want to say in my head, that's just another roadblock."* **[SOURCED]**
- **HN — *fuglede_*** (sameness): all Claude HTML looks the same — "ugh another one." **[SOURCED]**

**The synthesis both sides converge on**
- HN consensus (paraphrased in the thread summary): **HTML excels for *deliverables* (specs, reports, dashboards); Markdown is better for *collaboration* and *iteration*.** The word "unreasonably" reflects surprise that models handle markup volume without degradation — not that HTML is novel. **[SOURCED — HN]**
- The lifecycle rule (AgentMail): **"format should follow intended artifact lifespan and surface, not be standardized universally."** **[SOURCED]**

**The standards-proliferation warning (applies to any *new* format, including GEML)**
- **XKCD 927 ("Standards")**: attempts to unify 14 competing standards yield 15. The canonical rebuttal to "let's introduce a new format." Cited here because it is the *first* objection GEML will meet. **[SOURCED — xkcd.com/927]**

---

## 5. Implications & open questions for GEML

**GEML in one line (per the brief):** a plain-text document format where *everything* is one typed, `#id`-addressable block, with build-time reference checking (`geml check`), self-contained version history (`.gemlhistory`), and targeted read/patch of a single block by id (`geml get/set #id`) so an agent needn't ingest a whole file.

### 5.1 Where GEML sits on the debate's axes — and which pains it addresses well **[SYNTHESIS, grounded in SOURCED axes]**

| Axis (from §2) | GEML's likely standing |
|---|---|
| Token cost | **Strong** if source is plain-text and terse like Markdown (not HTML-verbose). Block-addressable read/patch is the bigger lever: agents fetch/patch *one block* instead of a whole file — directly aligned with Anthropic's own "just-in-time retrieval" context-engineering guidance and the field's move to selective, on-demand context. **[SOURCED — Anthropic context-engineering; Sourcegraph]** |
| Source readability | **Good by design** (plain text) — sidesteps the central anti-HTML complaint ("only readable after rendering"). **[SOURCED analogue]** |
| Reviewability / clean diffs | **Strong** — plain text diffs cleanly; a built-in `.gemlhistory` is a *format-native* answer to "if it can't be reviewed it's a toy." **[SOURCED analogue — Kurtis Redux/AgentMail]** |
| Human co-authoring | **Good** if humans can hand-edit blocks in any editor (the *tmhrtly* "no re-prompt roundtrip" win). Watch: `geml set #id` must not make hand-editing *feel* mandatory. **[SOURCED — HN]** |
| Structural precision & addressability | **This is GEML's headline advantage.** Typed `#id` blocks give exactly the stable, named handles the discourse says Markdown lacks and HTML only accidentally provides (SPAs "lose link-addressability unless deliberately architected" — HN *apsurd*). Mirrors the coding-agent convergence on **exact-string / block-anchored edits** (`str_replace_editor`, `apply_patch`) being *more reliable* than line-number or whole-file edits. **[SOURCED — HN; dev.to file-editing benchmark; AG2]** |
| Parse determinism | **Opportunity** — GEML can adopt djot's principle: local, backtrack-free, unambiguous parsing. A hard spec here is a genuine edge over Markdown. **[SOURCED — djot]** |
| Verifiability of *content* (references) | **GEML's differentiated bet.** `geml check` (build-time reference checking) targets an axis the mainstream MD-vs-HTML debate barely touches. This is closer in spirit to reST/Sphinx cross-reference resolution and to compiler-style checking than to a "prettier Markdown." Lead with this, not with expressiveness. **[SYNTHESIS; reST/Sphinx analogue SOURCED]** |
| Rendering fidelity / interactivity | **Weakest, and that's fine.** GEML should *not* try to out-HTML HTML. If rich visuals/interactivity are needed, the honest play is compile-to-HTML for the *deliverable* while keeping GEML as the *reviewable, editable, checkable source* — matching the HN "deliverable vs source" split. **[SOURCED — HN]** |
| Security | **Strong** if GEML stays declarative/non-executable (no arbitrary JS in source). Preserves the "plain text is safe" property; if it ever compiles to interactive HTML, the XSS surface reappears at that boundary. **[SOURCED — AgentMail]** |
| Ecosystem / "renders everywhere" | **Weakest, structurally.** This is the hardest, most important criticism (§5.2). |

### 5.2 Criticisms from this debate that GEML MUST answer honestly **[SYNTHESIS, each grounded in a SOURCED objection]**

1. **XKCD 927 / "why a new format at all?"** The very first reaction will be that GEML is standard #15. **Honest answer needed:** name the *specific* axis Markdown/HTML/AsciiDoc structurally cannot serve — the evidence points to **`#id` addressability + build-time reference checking**, i.e. *verifiable, agent-patchable structure*, not prettiness. If the pitch drifts toward "nicer Markdown," 927 wins. **[SOURCED — xkcd 927]**

2. **The ecosystem / lingua-franca problem (the killer).** Markdown's decisive advantage is that it "renders natively" in GitHub, GitLab, Notion, Slack, browsers, Pandoc. A new format renders *nowhere* on day one. **Honest answer needed:** a frictionless `geml → Markdown/HTML` export and ideally GitHub/preview rendering, so GEML is the source-of-truth without forcing everyone else's tools to change. Every niche format (djot, AsciiDoc, org, Typst) is bottlenecked here. **[SOURCED — Kurtis Redux; format comparisons]**

3. **"Models don't emit it reliably."** HTML/Markdown win partly because they saturate training data. A brand-new syntax has ~zero. **Honest answer needed:** keep syntax minimal and regular (djot-like determinism helps models too); consider whether GEML is close enough to Markdown that few-shot / a short spec in context is enough for reliable emission; be ready to show the reference parser rejects malformed output (fast feedback loop). **[SOURCED — HN "models emit HTML well"; djot]**

4. **Co-authoring friction.** If editing a block realistically requires `geml set #id` tooling rather than opening the file and typing, GEML re-creates the exact "another roadblock" complaint leveled at HTML. **Honest answer needed:** hand-editing in a plain editor must remain first-class; `get/set` is an *agent optimization*, not the only path. **[SOURCED — HN tmhrtly]**

5. **Verbosity / token cost of the typing & id scaffolding.** Typed blocks + explicit `#id`s add characters. If a GEML doc is meaningfully heavier than the equivalent Markdown, it partially forfeits Markdown's headline win. **Honest answer needed:** measure it; keep block headers terse; and lean on the *net* token argument — patching one addressed block beats re-emitting a file, so whole-workflow token use can drop even if the file is slightly larger. **[SOURCED — token-cost discourse; Anthropic just-in-time retrieval]**

6. **"Attention dilution / more structure ≠ better output."** Redux's claim that verbose markup raises hallucination risk. **Honest answer needed:** the arXiv study cuts both ways — format effects are real but **model- and task-dependent**, and strong models are more robust. Don't over-claim that GEML *improves reasoning*; claim it improves *addressability, reviewability, and verifiability*. **[SOURCED — 2411.10541; Kurtis Redux]**

7. **Conflict-of-interest / hype skepticism.** The HTML piece drew "you benefit from more tokens" cynicism. A format authored by its own advocate will face the mirror image. **Honest answer needed:** publish the reference parser, the spec, and reproducible token/round-trip benchmarks; let `geml check` demonstrate a capability, not a vibe. **[SOURCED — Kurtis Redux/HN]**

### 5.3 Ideas from the discourse worth feeding into GEML's spec **[SYNTHESIS on SOURCED ideas]**
- **Adopt djot's parsing discipline explicitly**: linear-time, no-backtracking, *local* inline parsing, balanced/unambiguous delimiters. Bake determinism into the spec as a stated goal. Good for humans, tooling, *and* model emission. **[SOURCED — djot]**
- **Positioning = "source-of-truth, compile-to-deliverable."** Embrace the HN "deliverable vs collaboration source" split: GEML is the reviewable/checkable/patchable **source**; HTML (or MD) is a **generated view**. Don't compete on rendering. **[SOURCED — HN]**
- **Make `geml check` the marquee feature, framed as compiler-for-docs.** The whole MD-vs-HTML fight ignores *content verifiability*. Build-time reference checking is a real, demonstrable, novel axis — closest prior art is reST/Sphinx cross-references, but as a *first-class, standalone* guarantee it's distinctive. **[SOURCED — reST/Sphinx analogue; SYNTHESIS]**
- **Lean into just-in-time / addressable retrieval.** Anthropic's own context-engineering guidance and the field's direction favor agents pulling *the specific block they need*. `geml get #id` is a format-native primitive for exactly that — cite this as the strategic tailwind. **[SOURCED — Anthropic context-engineering; Sourcegraph; LangChain]**
- **Match the agent-editing grain that already works.** Coding agents converged on **exact-string / block-anchored** edits over line-number or whole-file diffs because they're more reliable. `#id`-scoped `set` is the document analogue — spec it so a patch targets one block deterministically. **[SOURCED — dev.to file-editing benchmark; AG2 apply_patch]**
- **Keep the source non-executable.** Preserve the "plain text is safe" property; if/when GEML compiles to interactive HTML, treat that as an explicit, opt-in trust boundary. **[SOURCED — AgentMail security point]**
- **Ship export + rendering early.** Treat "renders in GitHub / previews cleanly / round-trips to Markdown" as a P0 spec/tooling requirement, because ecosystem lock-out is the empirically decisive failure mode for challenger formats. **[SOURCED — format comparisons; Kurtis Redux]**

### 5.4 Open questions (unresolved by the discourse; GEML must decide) **[SYNTHESIS]**
- Is GEML terse enough that its per-file token overhead is negligible vs Markdown — and can you *show* the net-token win from block-patching?
- Can models emit valid GEML from a short spec + few-shot, or does it need fine-tuning to be reliable? (Determines adoption ceiling.)
- Does `#id` addressability survive real human editing (ids not going stale, not renamed, not duplicated)? What does `geml check` do about dangling/duplicate ids?
- Where's the boundary between "GEML source" and "compiled HTML deliverable," and who owns the interactivity/security at that seam?
- Is the honest scope "structured, verifiable, agent-friendly **prose/spec** documents" rather than "general Markdown replacement"? The narrower the wedge, the more 927-proof.

---

## 6. Sources

Primary article
- Thariq Shihipar / Anthropic — *Using Claude Code: The unreasonable effectiveness of HTML* — https://claude.com/blog/using-claude-code-the-unreasonable-effectiveness-of-html (fetched)

Reaction / discussion
- Simon Willison — *The Unreasonable Effectiveness of HTML* — https://simonwillison.net/2026/May/8/unreasonable-effectiveness-of-html/ (fetched)
- Kurtis Redux — *The Unreasonable Ineffectiveness of HTML* — https://kurtis-redux.medium.com/the-unreasonable-ineffectiveness-of-html-5bd01ae1e879 (fetched)
- Roger Wong — *What Humans Actually Read* — https://rogerwong.me/2026/05/what-humans-actually-read (fetched)
- Hacker News discussion thread — https://news.ycombinator.com/item?id=48071940 (fetched via summary)
- explainx.ai summary — https://explainx.ai/blog/unreasonable-effectiveness-html-claude-code-thariq-2026 (via search summary)
- Pasquale Pillitteri — *HTML vs Markdown in Claude Code* — https://pasqualepillitteri.it/en/news/2243/html-vs-markdown-claude-code-thariq-anthropic (via search summary)
- claudeai.dev — *Claude Code and the Unreasonable Effectiveness of HTML Artifacts* — https://claudeai.dev/blog/claude-code-html-artifacts/ (via search summary)

Format comparisons (Markdown vs HTML for LLMs/agents)
- AgentMail — *HTML vs Markdown for AI agents* — https://www.agentmail.to/blog/html-vs-markdown-for-ai-agents (fetched)
- Tarik Davis — *Markdown vs HTML for LLM Agents: The 2026 Format Showdown* — https://www.tarikdavis.co.uk/blog/markdown-vs-html-for-llm-agents-the-2026-format-showdown/ (fetched)
- web2md — *HTML vs Markdown for LLMs: I Wasted 67% of My Tokens* — https://web2md.org/blog/markdown-vs-html-for-llm (fetched)
- searchcans — *Markdown vs. HTML for LLM Context* — https://www.searchcans.com/blog/markdown-vs-html-llm-context-optimization-2026/ (via search summary)
- searchcans — *Why Markdown is the Preferred LLM Output Format in 2026* — https://www.searchcans.com/blog/markdown-llm-output-benefits/ (via search summary)
- releasepad — *HTML vs. Markdown: The Optimal Format for LLM Content Ingestion* — https://www.releasepad.io/blog/html-vs-markdown-the-optimal-format-for-llm-content-ingestion/ (via search summary)
- beam.ai — *HTML vs Markdown for AI Agents* — https://beam.ai/agentic-insights/html-vs-markdown-which-format-actually-makes-ai-agents-more-useful (via search summary)
- Digiday — *WTF is Markdown for AI agents?* — https://digiday.com/media/wtf-is-markdown-for-ai-agents/ (via search summary)

Alternative formats
- djot — repo & rationale — https://github.com/jgm/djot ; *Why Djot?* — https://php-collective.github.io/djot-php/guide/why-djot ; Jonas Hietala — *Blogging in Djot instead of Markdown* — https://www.jonashietala.se/blog/2024/02/02/blogging_in_djot_instead_of_markdown/ (via search summaries)
- CommonMark — https://commonmark.org/ (via search summary)
- adoc-studio — *AsciiDoc vs Markdown, LaTeX & reStructuredText (2026)* — https://www.adoc-studio.app/blog/why-asciidoc (via search summary)
- Dewan Ahmed — *Markdown, Asciidoc, or reStructuredText* — https://www.dewanahmed.com/markdown-asciidoc-restructuredtext/ (via search summary)
- Hyperpolyglot — *Lightweight Markup* — https://hyperpolyglot.org/lightweight-markup (via search summary)
- dasroot — *Markdown vs AsciiDoc vs reStructuredText* — https://dasroot.net/posts/2026/03/markdown-vs-asciidoc-vs-restructuredtext-choosing-right-markup-language/ (via search summary)
- Felix Pappe — *Structured Prompting for LLMs: YAML, JSON, XML or Plain Text?* — https://felix-pappe.medium.com/structured-prompting-for-llms-from-raw-text-to-xml-daf39b461f13 (via search summary)

Research
- Jia He et al. — *Does Prompt Formatting Have Any Impact on LLM Performance?* — arXiv 2411.10541 — https://arxiv.org/abs/2411.10541 (via search summary)

Agent file-editing & context engineering
- dev.to (ceaksan) — *I Benchmarked 5 File Editing Strategies for AI Coding Agents* — https://dev.to/ceaksan/i-benchmarked-5-file-editing-strategies-for-ai-coding-agents-heres-what-actually-works-1855 (via search summary)
- AG2 — *GPT-5.1 Apply Patch Tool* — https://docs.ag2.ai/latest/docs/blog/2025/12/22/GPT-5.1-Apply-Patch-Tool/ (via search summary)
- Fabian Hertwig — *Code Surgery: How AI Assistants Make Precise Edits* — https://fabianhertwig.com/blog/coding-assistants-file-edits/ (via search summary)
- Anthropic — *Effective context engineering for AI agents* — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents (via search summary)
- Sourcegraph — *Context Engineering: A Practical Guide for AI Agents (2026)* — https://sourcegraph.com/blog/context-engineering (via search summary)
- LangChain — *Context engineering in agents* — https://docs.langchain.com/oss/python/langchain/context-engineering (via search summary)

Cultural reference
- XKCD 927 — *Standards* — https://xkcd.com/927/ ; explain xkcd — https://www.explainxkcd.com/wiki/index.php/927:_Standards

**Source-reliability note.** Several of the format-comparison blogs are vendor/marketing content (AgentMail, web2md, beam.ai, searchcans, releasepad) with a stake in one answer; their *numbers* (token percentages, task-improvement percentages) come from single, often un-reproduced benchmarks and are treated as directional. The most independent evidence points are the peer-reviewed-style arXiv study (2411.10541), djot's design documentation (John MacFarlane), and the primary-source article + named individuals (Simon Willison, Kurtis Redux, Roger Wong).
