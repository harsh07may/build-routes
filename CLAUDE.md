# CLAUDE.md

Everything needed to understand, edit and publish this repository. Read it fully before changing anything; the rules here exist because each one was a real mistake at some point.

## What this repo is

Three learning guides plus a landing page, served as a static site on GitHub Pages. There is no build step, no framework and no package manager: every page is one self-contained HTML file.

| Path | What it is | Progress key (localStorage) |
|---|---|---|
| `index.html` | Landing page: bento grid linking to all guides, shows progress | reads the keys below |
| `python-fastapi/index.html` | Route 1: Python to FastAPI (capstone: URL shortener) | `pyfast:done:v1` |
| `ai-foundations/index.html` | Route 2: AI foundations, a concept crash course with SVG diagrams | `aifound:done:v1` |
| `ai-engineering/index.html` | Route 3: AI engineering (capstone: docs assistant) | `aieng:done:v1` |
| `README.md` | Public description of the repo | |
| `.nojekyll` | Tells GitHub Pages to serve files as-is. Do not delete. | |
| `publish.sh` | One-time script that created the repo and enabled Pages. Git-ignored. | |
| `CLAUDE.md` | This file | |

The learner's background: backend engineer (about 3 years, mostly .NET), comfortable with basic Python, moving into AI engineering. The goal of both guides is to let him build the capstone projects **himself, without code generators**.

## How a guide page works

All three guide files share the same CSS and JavaScript (each was cloned from the previous one). If you change shared styling or behaviour, change it in **all three** files. The foundations page additionally has the diagram styles (see Diagrams).

### Content structure

A guide is a list of **parts**, each containing **stops**. Everything in the navigation is generated from this markup:

```html
<section class="part" id="rag" data-title="Retrieval (RAG)" data-line="l3">
  <div class="part-head">
    <p class="part-num"></p>                 <!-- filled by JS: "Part N" -->
    <h2>Retrieval (RAG)</h2>
    <p class="goal">One sentence on what this part achieves.</p>
  </div>

  <article class="stop" id="rag-embed">
    <h3>Embeddings with Voyage</h3>       <!-- becomes the rail label -->
    <p>Short explanation.</p>
    <div class="code"><pre><code class="language-python">...escaped code...</code></pre></div>
    <div class="note"><p><strong>Watch out:</strong> one gotcha.</p></div>
  </article>
</section>
```

What the script at the bottom of each guide builds from that markup:

- the left rail (a transit-map line per part, a station per stop, highlighting the stop in view);
- the hero route map (one station per part with "x of y stops");
- the mobile top bar (progress segments and a Contents drawer);
- a "Mark done" button on every stop, saved to localStorage;
- part numbering ("Part 1", "Part 2", ...), in document order;
- a Copy button on every code block, and syntax highlighting via highlight.js.

So: **never hand-write navigation, numbering or progress UI.** Add or move sections and articles; the rest follows.

### Available building blocks inside a stop

- Paragraphs: `<p>`.
- Code: `<div class="code"><pre><code class="language-LANG">...</code></pre></div>`. Languages that work with the bundled highlight.js build: `python`, `bash`, `ini` (also for `.env` and `pyproject.toml` snippets), `yaml`, `json`, `sql`, `plaintext`.
- Callout: `<div class="note"><p><strong>Label:</strong> text</p></div>`. The label takes the part's colour. Use for gotchas and "why" explanations, at most one or two per stop.
- Table: wrap in `<div class="tbl-wrap"><table>...</table></div>` so it scrolls on mobile.
- Lists: `<ul>`/`<ol>` are fine inside stops when the content really is a list.
- Labeled files and changed lines (used by the route 1 capstone): put `data-file="app/db.py"` on the `.code` div, plus `data-state="new"` or `data-state="changed"` to show a label bar. On a changed file add `data-hl="3,5-8"` to the `<code>` element (1-based line numbers, ranges allowed) and the script highlights those lines. Generate the ranges with `difflib` against the previous version rather than counting by hand. The same CSS and JS are in all three guides.

### Diagrams (route 2)

Diagrams are hand-written inline SVG inside a figure. They inherit the part's colour and adapt to dark mode through CSS classes, so never hard-code colours:

```html
<figure class="fig">
  <div class="fig-scroll"><svg xml:space="preserve" viewBox="0 0 640 240" role="img" aria-label="Describe the whole diagram in one sentence.">
    <rect class="d-box" .../>   <!-- neutral box -->
    <rect class="d-acc" .../>   <!-- filled with the part colour; put text with class t-inv on it -->
    <rect class="d-soft" .../>  <!-- tinted with the part colour -->
    <line class="d-ln" ... marker-end="url(#arr)"/>  <!-- arrow; add d-dash for dashed -->
    <text x=".." y="..">Label</text>   <!-- classes: t-mut (secondary), t-mono (code, keeps spaces), t-inv -->
  </svg></div>
  <figcaption>One or two sentences: what to notice, and any simplification.</figcaption>
</figure>
```

Rules: keep the viewBox about 640 wide (up to about 820 for wide flows); the figure scrolls sideways on phones and shows a swipe hint. The arrow marker `#arr` is defined once in a hidden SVG at the top of the page; reuse it. Every SVG needs `role="img"` and an `aria-label`. Label numbers only when steps really are a sequence. After editing, render the page and look at it (see Checks), because overlapping text is the most common mistake.

### Colours

Each part has a line colour set by `data-line="lN"`. Colour tokens are defined three times in each file's `<style>`: the light `:root` block, the `@media (prefers-color-scheme: dark)` block, and the `:root[data-theme="dark"]` block. Each token also needs a selector rule like `[data-line="l6"]{--line:var(--l6)}`.

| Token | Light | Dark | Route 1 | Route 2 (foundations) | Route 3 (AI engineering) |
|---|---|---|---|---|---|
| l0 | #B63C7C | #E36FAB | Prerequisites | | |
| l1 | #2F6BDB | #5B8FF0 | Python for applications | How LLMs work | LLM API |
| l2 | #C98A0C | #E8AE33 | Toolkit | Claude and the API | Tools |
| l3 | #05927F | #2EC2AA | FastAPI | Structured output and tools | RAG |
| l4 | #D2413A | #F0665E | Persistence | Embeddings and vector search | Evals |
| l5 | #7A4AD8 | #A17DF2 | Capstone | RAG | MCP |
| l6 | #5F7A12 | #A6C24A | (not defined) | Agents, MCP and evals | Agents |
| l7 | #237BA6 | #5DB6E0 | (not defined) | Toolbox and setup | Production |
| l8 | #B85A1B | #F08F4E | (not defined) | (defined, unused) | Capstone |

To add a colour: add the token to all three blocks, add the `[data-line]` selector, and add it to the landing page's `:root` blocks and its `COLORS` map. Or skip all that and use `data-color="#hex"` on the section (it won't adapt to dark mode).

### Progress and stop ids

Saved progress is a list of stop **ids** under the guide's localStorage key. Therefore:

- **Never rename or reuse a stop id** once published; users lose their "done" marks. Change the `<h3>` text freely instead.
- Deleting a stop is fine; its id just becomes unused.
- New ids must be unique within the guide. Use the part's prefix: `py-`, `b-`, `tk-`, `fa-`, `db-`, `cap-` (route 1); `lm-`, `cl-`, `tu-`, `vx-`, `rg-`, `ag-`, `tb-` (route 2); `llm-`, `so-`/`tl-`, `rag-`, `ev-`, `mcp-`, `ag-`, `pr-`, `cap-` (route 3).
- Only bump a key (`v1` to `v2`) if you deliberately want to reset everyone's progress.

## The landing page

`index.html` is a bento grid: an intro tile, one large tile per guide (`a.guide`), and small `.note` tiles.

Each guide tile has `data-src` (folder) and `data-key` (localStorage key). At runtime it fetches the guide, reads its parts and stops, and redraws the colour line with progress fill plus "N parts, M stops" and "x of y done". If the fetch fails (for example when opened from disk), the static fallback line in the markup stays. Keep that fallback roughly in sync (one `<span>` per part, correct colours) and the fallback "N parts" text.

The grid has 6 columns. With three guides each tile uses `grid-column: span 2`; below 980px wide guide tiles go full width. To add a fourth guide: create `new-guide/index.html` (see "Create a new guide"), add another `a.guide` tile with its own `data-src` and `data-key`, renumber the "Route N" labels, and rebalance spans (for example two rows of `span 3`).

## Content rules

These come from direct feedback. Follow them in every edit.

1. **Concise, one idea and one working example per stop.** Explanations are a few sentences. No filler.
2. **Don't frame content around DSA** or the learner's background. Write for any capable developer.
3. **Cross-references by name, never by part number.** Write "see Responses and errors in the FastAPI part", not "Part 3". Numbers are generated and shift when parts are added.
4. **Capstone solution code.** Route 1's capstone (URL shortener) deliberately shows the code as it stands at the end of each step, at the author's request; it was run against tests before publishing, so re-test it if you change it. Other capstones have a spec, milestones and hints only, unless the author asks otherwise. Building blocks elsewhere are fine.
5. **Route 2 (foundations) is concepts first.** Explain with prose, tables and diagrams; code only where it makes an idea concrete (the cosine example) and in the setup stop. Anything build-oriented belongs in route 3.
6. **Raw first, framework second** (route 3). Teach the mechanism with the provider SDK or plain code, then show the framework version (LangChain, LangGraph, Qdrant) and the trade-off.
7. **Consistency inside a guide.** One pattern per concern: for example, services own database transactions; the in-memory store is `dict[str, str]`; routes stay thin. Don't introduce a second pattern without replacing the first.
8. **Accuracy over completeness.** Don't publish an API detail you haven't checked. Leave a value blank (like the zeroed price table) rather than guess.
9. **Writing style:** sentence case headings, plain verbs, American spelling, no all-caps labels, no "→" on link text. Prefer prose; use lists only for real lists.

## Verifying facts before you change them

Routes 2 and 3 depend on fast-moving products and libraries. Before editing anything version-sensitive, check the official source (links in each guide's footer) and update the "checked in <month year>" line in route 3's hero, the landing page footer and the README if you re-verify.

State as of the last check (September 2026):

| Topic | Fact the guide relies on | Where it appears |
|---|---|---|
| Claude Console | API keys at platform.claude.com, Settings → API keys; keys start `sk-ant-`, shown once; keys are scoped to a workspace | R2 Accounts, keys and cost |
| API billing | Billed per token, separate from claude.ai subscriptions | R2 Accounts, keys and cost |
| Model tiers | Haiku, Sonnet, Opus, with the Mythos tier above Opus | R2 Claude and Anthropic |
| Workflows vs agents | Anthropic's "Building effective agents" definitions: predefined code paths versus model-directed process and tool use | R2 Workflows and agents |
| MCP framing | Official docs' USB-C analogy; host, client (one connection per server), server | R2 MCP |
| Anthropic Python SDK (route 3) | v1.x; structured outputs use `output_config.format`; `messages.parse(..., output_format=PydanticModel)` still accepted by the Python helper | R3: LLM API, Tools, RAG answer, Evals judge |
| Anthropic features | Automatic prompt caching via top-level `cache_control`; strict tools need `additionalProperties: false`, max 20 strict tools; Citations can't combine with structured outputs; Batches API is 50% off | R3 |
| Model ids | `claude-sonnet-5`, `claude-haiku-4-5-20251001` used as examples | R3 `.env`, price table, LangChain example; R2 API diagram and setup check |
| Removed sampling params | Unconfirmed claim that SDK v1 dropped `temperature`/`top_p`/`top_k`; deliberately not mentioned | Nowhere, keep it that way until confirmed |
| Voyage | `voyage-4`, 1024 dimensions by default; voyage-4 family models are mutually compatible; reranker `rerank-2.5` | R3 RAG |
| pgvector | `pgvector.sqlalchemy.Vector`, HNSW with `vector_cosine_ops`, image `pgvector/pgvector:pg17` | R3 RAG |
| MCP | Spec 2026-07-28 (stateless core); Python SDK v2 with `from mcp.server import MCPServer`; `mcp dev`, `mcp run --transport streamable-http`; `from mcp import Client` | R3 MCP |
| LangChain / LangGraph | LangChain v1 `create_agent`, `init_chat_model("anthropic:...")`; LangGraph 1.x | R3 Agents |
| Langfuse | Python SDK v4, `get_client()`, `start_as_current_observation` | R3 Production |
| OpenTelemetry GenAI conventions | Still evolving; sources disagreed on stability | R3 Production note |

Written from general knowledge and **not** re-checked against docs at the time (verify first if a learner reports a problem): Voyage `rerank()` call shape, the Qdrant client calls, LangChain's `langchain.tools` import and `.text` on messages, the LangGraph `interrupt`/`Command` pattern, SSE streaming details.

Route 1 facts are stable (FastAPI, Pydantic v2, SQLAlchemy 2.0, Alembic, redis-py) but still verify anything you add.

## How to make common changes

### Edit text in a stop
Edit the HTML directly. If you change a heading, the rail updates automatically.

### Add a stop
Copy an existing `<article class="stop">`, give it a new unique id with the part's prefix, write the `<h3>` and content, and place it where it belongs in the sequence.

### Add a part
Copy a whole `<section class="part">`, give it a unique `id`, `data-title` (short, shown in rail and hero) and a `data-line` colour. Place it in order; numbering updates. Routes 2 and 3 have a comment marking where new parts go. Update the landing page's fallback line and "N parts" text, and the README table if the scope changed.

### Add or edit a code block
Code inside `<pre><code>` must be HTML-escaped: `<` as `&lt;`, `>` as `&gt;`, `&` as `&amp;`. Quotes don't need escaping. The easiest safe way is to write the snippet in a scratch file and escape it:

```bash
python3 -c "import html,sys; print(html.escape(open(sys.argv[1]).read(), quote=False))" snippet.py
```

Don't double-escape: an already-escaped `&lt;` pasted through the escaper becomes `&amp;lt;` and shows literally.

### Create a new guide
1. Copy the closest existing guide to `new-guide/index.html` (use `ai-foundations/index.html` if you need diagrams).
2. Change `<title>`, the rail link text (`.rail-home`), the hero `<h1>` and lede.
3. Change `const KEY = "aieng:done:v1"` to a new unique key.
4. Replace all `<section class="part">` blocks with the new content.
5. Update footers so all guides link to each other and to `../`.
6. Add a tile to the landing page and a row to the README table.

### Change shared styling or behaviour
Make the same change in both guide files. Keep the landing page visually consistent (same fonts, palette tokens and radii).

## Checks before committing

Run from the repo root:

```bash
# 1. Every page parses as HTML
python3 -c "import html.parser; [html.parser.HTMLParser().feed(open(f).read()) for f in ['index.html','python-fastapi/index.html','ai-foundations/index.html','ai-engineering/index.html']]; print('html ok')"

# 2. Every Python snippet is valid syntax (top-level await allowed)
python3 - <<'EOF'
import ast, html, re
for f in ["python-fastapi/index.html", "ai-foundations/index.html", "ai-engineering/index.html"]:
    src = open(f).read()
    for i, code in enumerate(re.findall(r'<code class="language-python">(.*?)</code>', src, re.S)):
        try:
            compile(html.unescape(code), f"{f}#{i}", "exec", flags=ast.PyCF_ALLOW_TOP_LEVEL_AWAIT)
        except SyntaxError as e:
            print(f, i, e)
print("python snippets checked")
EOF

# 3. No duplicate stop ids within a guide
for f in python-fastapi/index.html ai-foundations/index.html ai-engineering/index.html; do
  grep -o '<article class="stop" id="[^"]*"' "$f" | sort | uniq -d
done

# 4. No leftover placeholders or artifact links
grep -rn "REPO_URL\|REPO_PAGES_URL\|claude.ai/artifact" --include=*.html --include=*.md --exclude=CLAUDE.md . || echo "clean"

# 5. Every diagram is well-formed SVG
python3 - <<'EOF'
import re, xml.dom.minidom
src = open("ai-foundations/index.html").read()
for i, svg in enumerate(re.findall(r'<svg xml:space="preserve" viewBox.*?</svg>', src, re.S)):
    xml.dom.minidom.parseString(svg.replace("<svg ", '<svg xmlns="http://www.w3.org/2000/svg" ', 1))
print("svgs ok")
EOF

# 6. Preview locally (the landing page's progress needs http, not file://)
python3 -m http.server 8000   # then open http://localhost:8000
```

Then check by eye: light and dark mode, a narrow (phone-width) window, the rail and "Mark done" working on anything you added, and every diagram you touched (text overlapping lines or boxes is easy to miss in code).

External resources are limited on purpose: highlight.js from cdnjs and Google Fonts. Don't add others without a reason; the pages must keep working as plain files.

## Publishing

The first publish was done with `./publish.sh [repo-name]` (requires `gh auth login`). It filled in the `REPO_URL`/`REPO_PAGES_URL` placeholders, created the repo, pushed `main` and enabled GitHub Pages from the root of `main`.

After that, publishing is just:

```bash
git add -A
git commit -m "Describe the change"
git push
```

GitHub Pages redeploys automatically within a few minutes. Check progress under the repo's Actions tab ("pages build and deployment"). If the site doesn't update, confirm Settings → Pages still says "Deploy from a branch: main, / (root)", and that `.nojekyll` still exists.

If the repo is renamed, update the links in `index.html` (Source tile) and `README.md` (live site line); GitHub redirects the old repo URL but not the old Pages URL.

## Working on this repo with Claude

When asked to change content:

- Read the relevant part of the guide first, and match its existing patterns and style.
- Research version-sensitive facts against official docs before writing them; say what was checked and what wasn't.
- Keep the content rules above, especially: no part numbers in prose, stable stop ids, and capstone code only where rule 4 allows it.
- Run the checks section before declaring the change done.
- Update this file when you add a guide, a colour, a convention, or re-verify facts.
