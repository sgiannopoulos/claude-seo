![Claude SEO cover: a Claude Code command palette with /seo audit, schema, geo, content, and backlinks commands over a dark CRT panel](assets/cover.svg)

# Claude SEO: SEO Skill for Claude Code

**Claude SEO is an open-source SEO analysis plugin for [Claude Code](https://claude.ai/claude-code).** It runs 26 sub-skills and 19 specialist agents in parallel across technical SEO, content quality (E-E-A-T), Schema.org markup, AI search optimization (GEO), local SEO, e-commerce, and international SEO. Every audit produces a prioritized action plan with testable recommendations grounded in primary-source guidance from Google.

[![CI](https://github.com/sgiannopoulos/claude-seo/actions/workflows/ci.yml/badge.svg)](https://github.com/sgiannopoulos/claude-seo/actions/workflows/ci.yml)
[![Claude Code Skill](https://img.shields.io/badge/Claude%20Code-Skill-blue)](https://claude.ai/claude-code)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Tests](https://img.shields.io/badge/tests-410%20passing-brightgreen)](tests/)

> This fork removes promotional report footers, community upsells, and unrelated product pitches.
> Original copyright and license notices, contributor credits, and functional integrations are retained.
> Manual installers use this fork's `main` branch; set `CLAUDE_SEO_TAG` to choose another ref.

### Analysis approach

- **AI-search first.** Aligned with [Google's AI Optimization Guide](https://developers.google.com/search/docs/fundamentals/ai-optimization-guide). Question-based citability scoring, primary-source evidence on llms.txt, IPTC `TrainedAlgorithmicMedia` for AI-generated product images, agent-friendly page checks per [web.dev](https://web.dev/), and `/seo agentic` for agent readiness scored the way Lighthouse's Agentic Browsing category counts it.
- **Parallel execution.** Full site audits spawn up to 17 specialist agents simultaneously. Audit duration depends on site size, available data, and configured tools.
- **Testable recommendations.** Every recommendation carries the first-principle observation it rests on, its dependency relationships, an explicit "how would we know this failed?" check, and a leading indicator. See [Methodology](#methodology).

![Claude SEO /seo command demo in Claude Code terminal](screenshots/seo-command-demo.gif)

Run a full audit and watch parallel agents fan out across the site:

![Claude SEO /seo audit demo: parallel subagents producing a prioritized action plan](screenshots/seo-audit-demo.gif)

## Table of Contents

- [Installation](#installation)
- [Quick Start](#quick-start)
- [Commands](#commands)
- [Features](#features)
- [Sample Output](#sample-output)
- [Architecture](#architecture)
- [Methodology](#methodology)
- [What's New in v2](#whats-new-in-v2)
- [Limitations](#limitations)
- [Requirements](#requirements)
- [Uninstall](#uninstall)
- [Extensions](#extensions)
- [Documentation](#documentation)
- [FAQ](#faq)
- [License](#license)
- [Contributing](#contributing)
- [Attribution](#attribution)

## Installation

### Plugin Install (Claude Code 1.0.33+)

The fastest path. One-time marketplace add, then plugin install:

```bash
/plugin marketplace add sgiannopoulos/claude-seo
/plugin install claude-seo@sgiannopoulos-claude-seo
/seo setup
```

The explicit setup step creates an isolated Python environment in Claude's
persistent plugin data and installs Playwright Chromium. Check it at any time
with `/seo doctor`. No global Python packages or PATH shims are created.

### Manual Install (Unix / macOS / Linux)

```bash
git clone --depth 1 https://github.com/sgiannopoulos/claude-seo.git
bash claude-seo/install.sh
```

<details>
<summary>One-liner (curl, review then run)</summary>

```bash
curl -fsSL https://raw.githubusercontent.com/sgiannopoulos/claude-seo/main/install.sh > install.sh
cat install.sh        # review before running
bash install.sh
rm install.sh
```

</details>

### Windows (PowerShell)

```powershell
git clone --depth 1 https://github.com/sgiannopoulos/claude-seo.git
powershell -ExecutionPolicy Bypass -File claude-seo\install.ps1
```

> **Why `git clone` instead of `irm | iex`?** Claude Code's own security guardrails flag `irm ... | iex` as a supply chain risk: downloading and executing remote code without verification. The `git clone` approach lets you inspect `claude-seo\install.ps1` before running it.

## Quick Start

```bash
# Start Claude Code
claude

# Full site audit: parallel sub-agents produce a prioritized action plan
/seo audit https://example.com

# Deep single-page analysis: on-page elements, content quality, schema
/seo page https://example.com/about

# Schema markup audit: detect, validate, generate
/seo schema https://example.com

# AI search optimization: passage citability + primary-source-aligned recommendations
/seo geo https://example.com

# Generate a sitemap with industry templates
/seo sitemap generate
```

## Commands

![Claude SEO sub-skill ecosystem: 26 modules grouped into 8 categories (audit, content, schema, technical, AI search, local + maps, commerce + intl, extensions) around the central orchestrator](assets/sub-skills.svg)

34 user-invocable `/seo` commands across the orchestrator, its sub-skills, and 9 MCP extensions. Full reference in [docs/COMMANDS.md](docs/COMMANDS.md).

| Command | Description |
|---------|-------------|
| `/seo setup` | Create or refresh the isolated Python runtime and Chromium |
| `/seo doctor` | Check runtime readiness without changing the system |
| `/seo audit <url>` | Full website audit with parallel sub-agent delegation |
| `/seo page <url>` | Deep single-page analysis |
| `/seo technical <url>` | Technical SEO audit across 9 categories |
| `/seo content <url>` | E-E-A-T and content quality analysis |
| `/seo content-brief <topic>` | Detailed content brief: target keywords, outline, internal links |
| `/seo schema <url>` | Detect, validate, and generate Schema.org markup |
| `/seo geo <url>` | AI Overviews / Generative Engine Optimization |
| `/seo agentic <url>` | Agent readiness: Lighthouse Agentic Browsing X/N, AI agent access, llms.txt, Markdown, WebMCP |
| `/seo sitemap <url \| generate>` | Analyze or generate XML sitemaps |
| `/seo images <url>` | Image optimization analysis |
| `/seo plan <type>` | Strategic SEO planning (saas, local, ecommerce, publisher, agency) |
| `/seo programmatic <url>` | Programmatic SEO analysis and planning |
| `/seo competitor-pages <url>` | Competitor comparison page generation |
| `/seo local <url>` | Local SEO analysis (GBP, citations, reviews, map pack) |
| `/seo maps [command]` | Maps intelligence (geo-grid, GBP audit, reviews, competitors) |
| `/seo hreflang <url>` | Hreflang / i18n SEO audit and generation |
| `/seo google [command]` | Google SEO APIs (GSC, PageSpeed, CrUX, Indexing, GA4, PDF reports) |
| `/seo backlinks <url>` | Backlink profile analysis (Moz, Bing, Common Crawl) |
| `/seo cluster <keyword>` | SERP-based semantic clustering |
| `/seo sxo <url>` | Search Experience Optimization (page-type, user stories, personas) |
| `/seo drift baseline \| compare \| history <url>` | SEO drift monitoring with SQLite snapshots |
| `/seo ecommerce <url>` | E-commerce SEO and marketplace intelligence |
| `/seo flow [stage]` | FLOW framework prompts (CC BY 4.0, evidence-led) |
| `/seo image-gen [use-case]` | AI image generation for SEO assets (extension) |
| `/seo seranking [command]` | AI Share-of-Voice across ChatGPT, Gemini, Perplexity, AI Overviews, AI Mode (extension) |
| `/seo profound [command]` | LLM citation tracking with time-series data (extension) |
| `/seo bing [command] <url>` | Bing Webmaster Tools + IndexNow URL submission (extension) |
| `/seo unlighthouse <url>` | Multi-page Lighthouse runner, runs locally (extension) |

Every extension command is documented in [docs/COMMANDS.md](docs/COMMANDS.md).

## Features

### What Core Web Vitals does Claude SEO check?

Claude SEO measures the current three Core Web Vitals: **LCP** (Largest Contentful Paint, target under 2.5s), **INP** (Interaction to Next Paint, target under 200ms), and **CLS** (Cumulative Layout Shift, target under 0.1). [INP replaced FID](https://web.dev/articles/inp) on March 12, 2024; FID was removed from Chrome's field-data tools (CrUX API, PageSpeed Insights) on September 9, 2024 (Lighthouse is a lab tool and never reported FID), and Claude SEO never references FID. Field data comes from the Chrome User Experience Report (CrUX) when available; lab data falls back to Lighthouse via PageSpeed Insights. LCP can be decomposed into subparts (TTFB, load delay, load duration, render delay) via the `/seo google` CrUX integration to localize bottlenecks. Mobile and desktop are measured separately. CrUX History (25-week trend) is included in the Tier 0 free credential set.

### How does Claude SEO assess E-E-A-T?

E-E-A-T (Experience, Expertise, Authoritativeness, Trustworthiness) is evaluated against the Search Quality Rater Guidelines, last updated September 2025 with YMYL expanded to include political and social topics. Experience signals: original research, case studies, first-hand photos. Expertise: author credentials and topical depth. Authoritativeness: external citations and brand mentions. Trustworthiness, the most heavily weighted of the four: contact info, secure HTTPS, transparent corrections, date stamps. Before scoring sub-factors, Claude SEO applies Google's own Who / How / Why heuristic from the [helpful-content guide](https://developers.google.com/search/docs/fundamentals/creating-helpful-content). Generative AI content is fine if it meets Search Essentials; it crosses into spam when used to scale low-value pages, which `seo-content humanize` and `seo-content verify` are designed to detect.

### What Schema.org types does Claude SEO support?

JSON-LD is the preferred format (Google's stated preference). Claude SEO detects, validates, and generates the active Schema.org types documented in [skills/seo/references/schema-types.md](skills/seo/references/schema-types.md), including organization, article, product, local, event, job, course, software/application, service, Q&A, and video patterns. FAQPage: Google stopped showing FAQ rich results for all sites on May 7, 2026; it has no Google rich-result benefit. Keep it only for non-Google or internal semantics if needed. Deprecated and never recommended: HowTo (rich results removed September 2023), SpecialAnnouncement (July 2025), ClaimReview, VehicleListing, EstimatedSalary, LearningVideo, CourseInfo carousel (all retired June 2025). Replacement guidance: [skills/seo-schema/references/deprecated-types-2024-2026.md](skills/seo-schema/references/deprecated-types-2024-2026.md).

### How does Claude SEO optimize for AI search?

Aligned with [Google's AI Optimization Guide](https://developers.google.com/search/docs/fundamentals/ai-optimization-guide), which says that "optimizing for generative AI search is optimizing for the search experience, and thus still SEO." AI Overviews and AI Mode are grounded in the same ranking systems as classic Search; pages must be indexed, eligible for snippet display, and included in the Search Console generative AI setting (the default) to appear in any AI feature. Claude SEO scores passage citability (self-contained answer blocks; ~130-170 words is a third-party heuristic, not a Google rule), question-based heading hierarchy, attribution density, structured data coverage, and entity presence across Wikipedia, Reddit, YouTube, and LinkedIn. The `seo-geo` skill includes evidence-based reframes of three popular myths: llms.txt is not currently a citation lever ([primary-source evidence](skills/seo-geo/references/llmstxt-evidence.md)), content chunking is not required, and AI-specific keyword rewriting is unnecessary because synonym understanding is sufficient.

### Which Google SEO APIs does Claude SEO integrate with?

A 4-tier credential system lets you start with zero keys and add data as needed. Every tier delivers real value at its level:

| Tier | Credentials | APIs Unlocked |
|------|------|------|
| 0 | API key | PageSpeed Insights, CrUX, CrUX History (25-week trends) |
| 1 | + OAuth or Service Account | + Search Console (queries, URL Inspection, sitemap status), Indexing API |
| 2 | + GA4 property config | + GA4 organic traffic, top landing pages, device / country breakdown |
| 3 | + Ads developer token | + Keyword Planner search volume and competition data |

PDF reports are generated via [WeasyPrint](https://weasyprint.org/) (A4 layout) with matplotlib charts at 200 DPI. Run `/seo google setup` for the credential wizard. All credentials live under `~/.config/claude-seo/` with `0o600` permissions; nothing is checked into the repo.

### How does Claude SEO handle local SEO?

Three layers. **Google Business Profile signals**: categories, hours, photos, posts, products, attributes. **NAP consistency** across citations: name, address, phone matched against major directories with deviation flagging. **Review intelligence**: rating trends, sentiment, response coverage. For multi-location businesses, Claude SEO enforces a 30-page warning threshold and a 50-page hard stop to prevent doorway-page violations (configurable). The `/seo maps` workflow adds geo-grid rank tracking, GBP profile auditing, and competitor radius mapping. Local schema generation covers `LocalBusiness` with all required and recommended properties (geo coordinates, opening hours, areaServed). v2 added a GBP deprecation linter that detects retired chat-field references and `.business.site` URLs.

## Sample Output

Claude SEO writes real markdown reports as its primary deliverable. The following illustrative schema report shows the output structure, headers, and grading format.

<details>
<summary><code>SCHEMA-REPORT.md</code>: illustrative schema audit</summary>

```markdown
# Schema Markup Report: example.com/about

**URL:** https://example.com/about
**Date:** 2026-02-09
**Format Detected:** JSON-LD (3 blocks) | No Microdata | No RDFa

---

## Summary

| Metric | Value |
|--------|-------|
| **JSON-LD Blocks** | 3 |
| **Schema Types** | Organization, WebSite, SoftwareApplication |
| **Critical Issues** | 2 |
| **Warnings** | 5 |
| **Passed Checks** | 18 |
| **Overall Grade** | B+ (solid foundation, actionable gaps) |

---

## Existing Schema Validation

### 1. Organization (`@id: #organization`)

| Property | Value | Status | Notes |
|----------|-------|--------|-------|
| `@context` | https://schema.org | Valid | |
| `@type` | Organization | Valid | Active type |
| `@id` | https://example.com#organization | Good | Enables cross-referencing |
| `name` | Example Company | Valid | |
| `description` | Present, 200+ chars | Good | Descriptive and keyword-rich |
| `url` | https://example.com | Valid | Absolute URL |
| `logo` | ImageObject with @id, url, width, height, caption | Excellent | Well-structured |
| `foundingDate` | "2024" | Imprecise | Year-only accepted but ISO 8601 preferred |
| `areaServed` | "Worldwide" | Text | Works but `GeoShape` is more semantic |
| `contactPoint` | email + contactType | Valid | Consider adding `telephone` |
| `founder` | 1 Person (Alex Morgan) | Incomplete | Page describes two co-founders; second missing |
| `sameAs` | 5 social profiles | Good | GitHub, X, LinkedIn, YouTube, Reddit |
| `knowsAbout` | 6 topics | Good | Relevant topical signals |

**Critical Issue:** The `founder` property only includes Alex Morgan. Taylor Reed (Co-Founder & Technical Director) is displayed on the page but absent from the schema. This creates a content-schema mismatch that can confuse search engines.
```

</details>

Other audit outputs follow the same shape: `FULL-AUDIT-REPORT.md` (umbrella audit), `GEO-ANALYSIS.md` (AI-search readiness), `LOCAL-SEO-ANALYSIS.md` (GBP and citations), and a production PDF via WeasyPrint + matplotlib (cover, TOC, executive summary, data sections, recommendations, methodology, roughly 32 A4 pages for a full site audit).

## Architecture

![Claude SEO audit signal flow: /seo audit enters the orchestrator, fans out to 26 sub-skills and up to 17 parallel audit agents, and converges through the scoring engine into a prioritized report](assets/signal-flow.svg)

The plugin follows the [Agent Skills standard](https://docs.claude.com/en/docs/claude-code/skills) with a 3-layer architecture (directive, orchestration, execution). Skills and agents are auto-discovered from `skills/seo-*/` and `agents/seo-*.md`. The orchestrator (`skills/seo/SKILL.md`) handles industry detection (SaaS, local, ecommerce, publisher, agency), parallel sub-agent dispatch up to 17 simultaneously, and synthesis through the [10-principle framework](#methodology) before emitting the action plan. Full architecture: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

## Methodology

![Claude SEO 10-principle methodology: PERCEIVE, ANALYZE, VALIDATE, and ACT phases with 10 principles arranged by quadrant](assets/framework.svg)

Every audit walks 10 principles grouped into four phases. Each emitted recommendation carries four fields: the first-principle observation it rests on, its dependency relationship to other recommendations, a "how would we know this failed?" check, and a leading indicator to monitor.

| Phase | Principles | What it does |
|---|---|---|
| **PERCEIVE** | OBSERVE (external) · OBSERVE (internal) · LISTEN | Collect raw signals; audit your own assumptions; read what the SERP, the brand voice, and the community actually say |
| **ANALYZE** | THINK · CONNECT (lateral) · CONNECT (system) | Reduce to first principles; find non-obvious cross-skill links; sequence into a dependency graph |
| **VALIDATE** | FEEL · ACCEPT | Pressure-test against UX, brand voice, operator capacity; surface falsifiability |
| **ACT** | CREATE · GROW | Ship the artifact; set the feedback loop for the next audit |

Full methodology: [skills/seo/references/thinking-framework.md](skills/seo/references/thinking-framework.md).

## What's New in v2

- **v2.1.0 (May 2026): currency refresh.** May 2026 core update, Google I/O 2026 (custom version of Gemini 2.5 powers AI Mode), FAQ rich results retired 2026-05-07 (QAPage remains the type for genuine Q&A pages, FAQ markup itself just no longer yields rich results).
- **v2.2.0 (June 2026): security + portability.** Installer credential-injection fix, SSRF authority-confusion bypass closed, Google API keys moved to the `X-Goog-Api-Key` header, secret-scan CI gate, Windows/macOS fixes; suite at 326.
- **v2.2.1 (June 2026): Google-currency reconfirmation + full command audit.** Lighthouse 13.4.0 guidance, including the Agentic Browsing category (added in 13.2.0, on by default since 13.3.0), Google Search ignores llms.txt, an internally-reweighted E-E-A-T scorecard (Trust highest, per Google's 'trust is most important'); every `/seo` command and subcommand audited and COMMANDS.md brought to 100% coverage.
- **v2.2.2 (July 2026): full-review maintenance.** Corrected GBP Q&A handling, AI Mode model naming, image-model IDs, hook input behavior, and added a strict reference-graph consistency gate.
- **v2.2.3 (July 2026): prompt-hygiene alignment.** Normalized emphasis and punctuation across the prompt surface without changing behavior, routing, or output contracts.
- **v2.2.4 (July 2026): community maintenance.** Added the managed cross-platform runtime and safe sitemap discovery, repaired GSC pagination and totals, replaced removed Bing endpoints, fixed extension and Windows portability gaps, and reconciled every open issue and pull request.
- **v2.2.5 (August 2026): reliability and Google-currency hardening.** Fixed manual-install data packaging and lxml runtime imports, hardened JSON-LD graphs and rendered-page accessibility analysis, enforced managed-runtime command references, and refreshed Google Search and Lighthouse guidance through August 25.
- **v2.2.6 (September 2026): security and hosted-install patch.** Closed a cache-path traversal and a WHOIS referral SSRF, refused the RFC 6598 range, moved the launcher to `scripts/` so the claude.ai-hosted marketplace accepts the plugin, raised dependency floors past PYSEC-2026-3940, locked the cost ledgers, and added Windows, macOS, and pip-audit CI.
- **v2.3.0 (September 2026): Google currency and community fixes.** Refreshed Google guidance through 2026-09-10 (EEA site-reputation enforcement, regional aggregator and supplier units, AI Mode travel, CrUX August), added the `CLAUDE_SEO_LOCAL_TARGETS` allowlist and proxy validation, hardened the JSON-LD hook and Unicode watermark stripping, corrected AI crawler guidance, unified `fetch_page --json`, fixed Unlighthouse flags, raised audit agent turn budgets, and landed 12 more community PRs.
- **v2.3.1 (September 2026): agents on Opus and community follow-ups.** Five judgment-heavy agents run on Opus, Keywords Everywhere joins as a free backlinks fallback, setup failures show the failing stage's output, the remaining extension installers write `~/.claude.json` atomically, and the v2.3.0 templated-metadata script is registered with the launcher.
- **v2.4.0 (September 2026): agent readiness and audit fixes.** New `/seo agentic` scores Lighthouse's Agentic Browsing category exactly as Lighthouse counts it and audits AI agent access, llms.txt, Markdown delivery, `ai-catalog.json` and WebMCP; extension installers keep secrets off the command line and never wipe a malformed config; fixes #312, #314 and #317; facts re-verified against primary sources.
- **v2.4.1 (September 2026): Google-currency patch.** Adds the September 2026 spam update and every Google change through 2026-09-28 to the ledger, re-verifies about 30 facts against primary sources, labels third-party claims as such (including the SAFE detector and the "link spam excluded" claim), reports the Lighthouse version PSI ran, splits oversized references, and adds regression tests for each correction.
- **v2.4.2 (October 2026): seo-cockpit and Google sign-in.** Adds `seo-cockpit`, an optional mods companion plugin (Claude Code 2.1.287+): a spend guard that holds paid SEO API calls against your budget, a live audit band with a receipt, an economy mode, and `/seo-cockpit`, a pane with Search Console, rankings, Core Web Vitals, the audit, Maps and spend for a default site from any folder. Google APIs can now use your own gcloud sign-in, schema-hook diagnostics reach Claude, and the LLM-mentions price is corrected.

## Limitations

Two real boundaries worth being upfront about.

**Heavy client-side hydration timing.** The headless renderer handles most SPAs out of the box (`--render auto` detects empty `<div id="root">` shells and switches to Playwright). Edge cases that still produce noisy findings: pages with hydration tied to scroll position past the fold, pages that fetch critical content after user interaction (modal opens, tab clicks), pages with race-condition-prone third-party widget mounts. For these, manually triggering the `seo-visual` subagent and comparing its Playwright snapshot to the raw-HTML subagents' findings is the recommended workflow.

**Local-only without enrichment.** The free tier makes no third-party API calls by default (audits still fetch the target URLs you point them at). Adding Google API credentials (Tier 0 through 3) unlocks real field data and live indexation status; without them, Core Web Vitals are lab estimates only and indexation is inferred from page-level signals. Adding optional MCP extensions similarly unlocks competitive and AI-citation data but requires their respective accounts.

## Requirements

**Agent models.** Five judgment-heavy agents (`seo-content`, `seo-geo`, `seo-sxo`,
`seo-cluster`, `seo-drift`) run on Opus since v2.3.1; the other thirteen run on
Sonnet. A full `/seo audit` therefore costs more than it did on v2.3.0. To change
a model, edit the `model:` line in the agent's frontmatter under `agents/`.


- Python 3.10+ (Python 3.10 reaches end of life in October 2026; use 3.11 or newer for new installs. A later minor release will raise the floor.)
- Claude Code CLI
- Optional: Playwright Chromium: install.sh offers to install it (you can skip the prompt); needed only for SPA rendering and screenshots
- Optional: Google API credentials for enriched CWV / GSC / GA4 data (see `/seo google setup`)

## Uninstall

```bash
git clone --depth 1 https://github.com/sgiannopoulos/claude-seo.git
bash claude-seo/uninstall.sh
```

<details>
<summary>One-liner (curl)</summary>

```bash
curl -fsSL https://raw.githubusercontent.com/sgiannopoulos/claude-seo/main/uninstall.sh | bash
```

</details>

## Extensions

Optional MCP servers add live data to the audit pipeline. The plugin core works without any of them; each extension ships its own installer and docs under `extensions/<name>/`.

### Banana: AI image generation

SEO image generation (OG previews, blog heroes, product photos, infographics) via the [Claude Banana](https://github.com/AgriciDaniel/banana-claude) Creative Director pipeline.

```bash
./extensions/banana/install.sh
/seo image-gen og "Professional SaaS dashboard"
```

Full Banana docs: [extensions/banana/README.md](extensions/banana/README.md).

### SE Ranking, Profound, Bing Webmaster, Unlighthouse (new in v2)

Added in v2:

- **SE Ranking:** AI Share-of-Voice across ChatGPT, Gemini, Perplexity, AI Overviews, AI Mode
- **Profound:** LLM citation tracker with time-series data
- **Bing Webmaster:** Bing Webmaster Tools plus IndexNow unified
- **Unlighthouse:** MIT-licensed multi-page Lighthouse runner

Setup walkthroughs live under `extensions/<name>/docs/`; integration notes: [docs/MCP-INTEGRATION.md](docs/MCP-INTEGRATION.md).

## Documentation

- [Installation Guide](docs/INSTALLATION.md)
- [Commands Reference](docs/COMMANDS.md): every `/seo` command in depth
- [Architecture](docs/ARCHITECTURE.md): 3-layer design, auto-discovery, parallel dispatch
- [Migration v1 → v2](docs/MIGRATION-v1-to-v2.md): breaking changes, six phases of work
- [MCP Integration](docs/MCP-INTEGRATION.md): integration notes; extension setup lives under `extensions/<name>/docs/`
- [Troubleshooting](docs/TROUBLESHOOTING.md)
- [Contributors](CONTRIBUTORS.md): community credits

## FAQ

### What is Claude SEO?

Claude SEO is an open-source SEO analysis plugin for Claude Code. It runs 26 sub-skills and 19 specialist agents in parallel across technical SEO, content quality, Schema.org markup, AI search optimization, local SEO, e-commerce, and international SEO. Audits produce a prioritized action plan where each recommendation carries the first-principle observation it rests on, its dependency relationship to other recommendations, a "how would we know this failed?" check, and a leading indicator. The plugin is MIT-licensed, ships zero proprietary tracking, and works without third-party API enrichment; audits still contact the target URLs you analyze. Aligned with [Google's AI Optimization Guide](https://developers.google.com/search/docs/fundamentals/ai-optimization-guide) and the September 2025 Quality Rater Guidelines.

### Does Claude SEO work on single-page applications (Next.js, React, Vue)?

Yes. v2 shipped a shared headless renderer (`scripts/render_page.py`) backed by Playwright Chromium. Audit subagents call `render_page.py --mode auto`, which auto-detects SPA hallmarks (empty `<div id="root">` shells, single bundle script, hydration markers) and switches to a rendered fetch. The lower-level `scripts/fetch_page.py` wrapper supports `--render auto` as an opt-in wrapper mode; its default is `--render never` for raw HTTP. Use `render_page.py --mode always` or `fetch_page.py --render always` to force rendering. Content extraction uses [trafilatura](https://github.com/adbar/trafilatura) for boilerplate removal. Publication dates come from [htmldate](https://github.com/adbar/htmldate). Known nuance: pages with scroll-bound hydration or post-interaction content fetches still produce noisy findings; see the [Limitations](#limitations) section for the recommended `seo-visual` cross-check workflow on those edge cases.

### What Google APIs does Claude SEO use, and are they required?

None are required. Claude SEO is fully functional with zero API keys. A 4-tier credential system lets you upgrade gradually: Tier 0 (API key only) unlocks PageSpeed Insights, CrUX, and CrUX History (25-week trend data). Tier 1 (+ OAuth or service account) adds Search Console with queries, URL Inspection, sitemap status, and the Indexing API for eligible JobPosting pages or BroadcastEvent in VideoObject pages; the API does not guarantee indexing. Tier 2 (+ GA4 property config) adds organic traffic, top landing pages, and device / country breakdowns. Tier 3 (+ Ads developer token) adds Keyword Planner search volume and competition data. The credential setup wizard runs via `/seo google setup`. All credentials live under `~/.config/claude-seo/` with `0o600` file permissions; nothing is checked into the repo and nothing is transmitted beyond Google's own endpoints.

### Is Claude SEO free?

Yes. MIT licensed, fully open source, no per-domain pricing, no telemetry, no API quotas imposed by the plugin itself. The core plugin and all 26 sub-skills work without any paid service. Some optional MCP extensions wrap paid services where you bring your own account credentials; their use is opt-in and the plugin works fully without them. Google APIs (PageSpeed Insights, Search Console, Indexing, GA4) are free from Google with normal account quota limits and require your own credentials. If you want commercial support or enterprise features beyond the open-source plugin, that is not part of this project.

### How is Claude SEO different from regular SEO tools when it comes to AI search?

Most SEO tools treat AI search as a separate optimization discipline. Claude SEO follows [Google's own position](https://developers.google.com/search/docs/fundamentals/ai-optimization-guide) that optimizing for generative AI search is "still SEO". AI Overviews and AI Mode are grounded in the same ranking systems as classic Search; the eligibility floor is normal indexation. Claude SEO scores passage citability (self-contained answer blocks), question-based heading hierarchy, attribution density, and entity presence across Wikipedia, Reddit, YouTube, and LinkedIn. It explicitly rejects three influencer myths: llms.txt as a citation lever, content chunking for AI, and AI-specific keyword rewriting. For commerce sites, Claude SEO audits the IPTC `TrainedAlgorithmicMedia` requirement on AI-generated product images per Google Merchant Center policy.

## License

MIT License. See [LICENSE](LICENSE) for details.

## Contributing

Contributions welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before submitting PRs and include the tests or checks you ran in the PR description.

---

## Attribution

Originally created by [AgriciDaniel](https://github.com/AgriciDaniel).
See [LICENSE](LICENSE) and [CONTRIBUTORS.md](CONTRIBUTORS.md) for license and contributor credits.
