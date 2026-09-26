# Build routes

Two hands-on learning guides for going from Python basics to shipping AI systems. Each guide is a sequence of short stops, one idea and one working example each, ending in a capstone project you build yourself.

**Live site:** https://harsh07may.github.io/build-routes/

| Route | Covers | Capstone |
|---|---|---|
| [1. Python to FastAPI](python-fastapi/) | Python essentials for applications, type hints, Pydantic, async, FastAPI, SQLAlchemy 2.0 (async), Alembic, Redis | URL shortener |
| [2. AI engineering](ai-engineering/) | Anthropic SDK, structured outputs, tool calling, RAG with Voyage embeddings and pgvector (plus Qdrant), evals, MCP, agents with LangChain and LangGraph, production concerns | Docs assistant |

Route 2 assumes the FastAPI, Postgres and pytest skills from route 1.

## Using the guides

- Open the live site, or open `index.html` locally in a browser.
- Mark stops as done to track progress. Progress is stored in your browser's `localStorage` and never leaves your machine.
- Code examples are building blocks. The capstones deliberately contain no solution code.

## Structure

```
index.html                  landing page
python-fastapi/index.html   route 1
ai-engineering/index.html   route 2
```

Each guide is a single self-contained HTML file with no build step. Syntax highlighting (highlight.js) and fonts load from public CDNs.

## Adding a part or stop

Navigation, numbering and progress are generated from the page structure:

```html
<section class="part" id="unique-id" data-title="Part title" data-line="l3">
  <div class="part-head"><p class="part-num"></p><h2>Part title</h2><p class="goal">One line.</p></div>
  <article class="stop" id="unique-stop-id"><h3>Stop title</h3> ... </article>
</section>
```

Use `data-line` with a color defined in the page's CSS, or `data-color="#hex"`. Stop ids must stay stable, since saved progress is keyed by them.

## Accuracy

Library APIs were checked against official documentation in September 2026. The AI tooling in route 2 (Anthropic SDK, MCP SDK v2 and the 2026-07-28 spec, LangChain and LangGraph) changes quickly. Pin versions in your own projects and check the linked docs when something doesn't match.
