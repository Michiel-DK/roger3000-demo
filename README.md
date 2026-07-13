# roger3000 — an investor-intelligence engine that shows its work

**[▶ View the live demo →](https://michiel-dk.github.io/roger3000-demo/)**

A production AI platform for high-stakes investment diligence — sourcing, matching,
and screening — built around one hard idea: **the engine fact-checks every claim it
writes against its own evidence before a human ever sees it, and measures how often
it gets that right.**

This repo hosts a self-contained, scrollytelling **demo** of the product (below).
The platform's source code is private; this page is the showcase.

> All names, firms, people and figures in the demo are generated demo data
> (a fictional client). The only real numbers on the page are the three
> claim-auditor calibration figures.

---

## What it does

Two halves, both domain-agnostic — they run on whatever corpus the engine is pointed at:

- **Match** — given a thesis, mandate, or pitch, score and rank entities against it by
  blending semantic similarity with structured relationship signals. Not keyword search,
  not a single embedding lookup — a **10-signal** score that's explainable signal-by-signal.
- **Verify** — every narrative claim the engine writes is fact-checked against the
  structured evidence it was given. Claims it can't ground are labelled and the
  fabricated ones are **stripped before they reach you**.

**Agentic on the outside, deterministic and audited on the inside.** You use it through a
conversational agent that runs the searches and assembles the answer end-to-end — but the
layers that *score a match* and *fact-check a claim* are deliberately deterministic and
audited, not a free-roaming model. Effortless where that helps; provable where a wrong
answer would cost you.

---

## The part I'm most proud of: measured groundedness

Most AI tools ask you to trust the output. This one **measures how often its own verifier
invents versus grounds a claim**, on a fixed hand-labelled test set, and holds a bar before
that verifier is trusted in production.

The auditor uses three verdicts, not two:

| Verdict | Meaning | What happens |
|---|---|---|
| `verified` | Grounded in structured evidence | Kept, with an evidence pointer |
| `unverifiable` | No source found — *may* be true | Kept, shown with a caveat |
| `fabricated` | Contradicts the evidence | **Stripped before you read it** |

Current calibration (57 hand-labelled fixtures — small, self-authored, honest):

| Metric | Result |
|---|---|
| False-positive rate (legit claims wrongly stripped) | **0%** |
| Fabricated recall (planted fabrications caught) | **100%** |

Presented as a **baseline, not a green-light** — an internal bar that's the *precondition*
for trusting the auditor, re-generated automatically as the engine changes so the number
never goes stale. It runs **inline on every production report**: each ships with a
per-claim fact-check panel and evidence pointers. The discipline of measuring and gating is
the point, not the percentage.

*A real caught example:* a generated report asserted "recent investments over the last 90
days focused on Cellular Agriculture…" — the firm's own data showed zero investments in
that window. Flagged `fabricated`, stripped, never shown.

---

## Engineering highlights

- **Ingestion pipeline** — a multi-phase pipeline turns unstructured websites, documents,
  news and exports into clean, structured, queryable records (site analysis → scrape →
  content-filter → LLM extraction → per-entity enrichment → QA → store), with resume/dedup
  guards so re-runs are cheap.
- **Semantic + relational search** — every record embedded in **pgvector**; entities and
  the edges between them resolved into a traversable relationship graph — the intelligence a
  flat database doesn't assemble. (Scale of the live dataset: ~1,200 firms, ~36k portfolio
  companies, ~124k knowledge-graph edges.)
- **10-signal explainable matcher** — semantic similarity + nine structured signals (news
  momentum, portfolio overlap, thesis-cluster fit, co-investment, similar-firm propagation,
  revealed-preference, hard-filter prior, …), each computed independently and summed, so any
  match is defensible signal-by-signal.
- **Claim auditor** (above) — LLM-graded verification with a deterministic calibration
  harness and a CI gate; expert 👍/👎 corrections promote into new fixtures (the flywheel).
- **Converged agentic chat** — one input; a router decides doc-Q&A vs a tool-bound
  capability and dispatches accordingly, streaming cited answers with a groundedness badge.
- **Public MCP server** — the whole capability surface exposed as tools an assistant
  (e.g. Claude) can orchestrate, with OAuth.
- **Full stack + shipped** — FastAPI backend, React frontend, PostgreSQL/pgvector, scheduled
  cron pipelines, deployed on Railway + Supabase with a staging→production promote flow.

---

## Tech

`Python` · `FastAPI` · `PostgreSQL` + `pgvector` · `React` / `TypeScript` · `Gemini`
(extraction, matching, verification) · `MCP` · `Railway` · `Supabase`

The demo page itself is a single self-contained HTML file: inline CSS + vanilla JS, zero
dependencies, no build step, scroll-triggered animation.

---

## Status — honest

**Shipped and live:** the ingestion pipeline, the vector + graph dataset, the 10-signal
matcher, evidence-backed reports, the claim auditor (running inline on every production
report, with the measured calibration + CI gate), the converged agentic chat, the public
MCP server, the 👍/👎 feedback flywheel, and a sales-pipeline triage/outcome-capture layer.

**In progress / roadmap:** pointing the same engine at a client's *own* corpus (vs the VC
dataset it runs on today), per-tenant data isolation so one engine serves many clients
walled-off, single-tenant deployable instances, and live CRM / mail / meeting connectors.
Latency is a real, ongoing investment — trust and relationship depth lead, speed follows.

---

_Built by Michiel De Koninck. Product source is private; this repository is the demo
showcase. Reach out if you'd like a walkthrough of the engineering behind it._
