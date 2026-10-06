# Improvement Tasks — Production AI Patterns

> Audit date: 2026-10-06. The audit covered the repo structure, `index.html`,
> `metadata.json`, the 12 article files, and the README.
> Priority: **P0** = broken or incorrect now · **P1** = high value ·
> **P2** = nice to have.
> Use `- [ ]` → `- [x]` to track progress.

---

## 1. Data integrity — `metadata.json` (P0)

- [ ] **P0 · Duplicate IDs.** Three articles share the ID `"1042"`:
  `predictive-token-budget-circuit-breaker`, `bleeding-tokens-silently`, and
  `context-filtered-gateway-decomposition`. Give each article a unique ID.
  The index shows the ID as `PATTERN-1042`, so readers currently see the
  same label on three cards.
- [ ] **P0 · Orphaned article.** `articles/context-rot-is-not-a-window-problem.html`
  exists but has no metadata entry, so it never appears on the site. Add the
  entry. Also note that the filename slug ("context rot…") doesn't match the
  page title ("Deploying Prompts Like Code Is a Trap"). Rename the file or
  retitle the page, and add a redirect stub if the URL was ever shared.
- [ ] **P0 · Drafts point to missing files.** `the-cost-gate.html`,
  `circuit-breaker-model-inference.html`, and
  `reading-the-pattern-language.html` are listed in the metadata but don't
  exist. Either write them (see §5) or move them to a `drafts` list.
- [ ] **P1 · Inconsistent schema.** Some entries use `type`, others use
  `format`. `research_window` appears as an array, as `"A to B"`, as
  `"A → B"`, and as `"A through B"`. Normalize every entry to the schema in
  `docs/master-prompt-v1.md` (STEP 5), with `research_window` as a
  two-element ISO array.
- [ ] **P1 · Formatting.** The file mixes 2-space and inline formatting
  (`},{` joins). Reformat it with `python3 -m json.tool --indent 2`.
- [ ] **P1 · Add `pillar` and `related` fields** to every entry, so the index
  can filter and articles can cross-link.
- [ ] **P2 · Tag vocabulary.** Merge near-duplicates (`circuit-breaker` vs.
  `circuit-breakers`, `cost` vs. `cost-governance`) and document the
  canonical list in `docs/`.
- [ ] **P2 · Pinned logic.** `deaf-at-the-wheel` and `unbounded-memory-trap`
  are pinned and both get the "START HERE" label. Choose one start-here
  article, or rename the label "FEATURED".

## 2. Automation and CI (P1)

- [ ] **P1 · Metadata validation workflow.** Add a GitHub Action (Python, no
  dependencies) that fails the PR when any of these hold:
  - the JSON is invalid
  - IDs or slugs are duplicated
  - a `published` entry points to a missing `path`
  - an HTML file in `articles/` has no metadata entry
  - a required key is missing or `research_window` is malformed
- [ ] **P1 · HTML checks.** Add `html-validate` or the W3C Nu checker plus a
  link checker (e.g. `lychee`) in CI for the article files.
- [ ] **P2 · Explicit Pages workflow.** The README says merges deploy
  automatically, but the repo has no `.github/workflows`. Document the
  Pages settings or add an `actions/deploy-pages` workflow.
- [ ] **P2 · PR template.** Add `.github/pull_request_template.md` with
  sections for the pattern, sources, the research window, and checklist
  items from the master prompt.
- [ ] **P2 · Trim `.gitignore`.** It is the stock Node template, which is
  irrelevant to a zero-dependency static site.

## 3. Site / UX — `index.html` (P1)

- [ ] **P1 · Filter by pillar and tag.** Add clickable tag pills and pillar
  chips that filter the grid client-side, synced to the URL hash so
  filtered views can be shared.
- [ ] **P1 · Search.** Add a client-side search over title, hook, and tags.
  `metadata.json` is small enough to search in memory.
- [ ] **P1 · Card ID bug.** `padId()` falls back to the array position when
  an ID is missing, which produces inconsistent labels. Use the metadata ID
  only, and drop the label when the ID is absent.
- [ ] **P1 · Escape metadata in templates.** `cardHTML` interpolates
  `title`, `hook`, and `tags` into `innerHTML` without escaping. Add an
  `esc()` helper.
- [ ] **P1 · `fetch` fallback.** Opening `index.html` from `file://` shows
  "Catalog is empty". Show a helpful message instead, e.g. "Serve with
  `python3 -m http.server`".
- [ ] **P2 · Pattern map.** Add a view that groups patterns by pillar, or a
  small graph that draws the `related` links between patterns.
- [ ] **P2 · Light mode.** Add a `prefers-color-scheme: light` token set.
- [ ] **P2 · Footer copy.** "the pattern-language layer of an eight-repo
  portfolio" is overwritten by the site description at runtime. Decide
  which text you want, and link the portfolio if it stays.

## 4. SEO, sharing and discoverability (P1)

- [ ] **P1 · Meta descriptions.** Only 1 of 12 articles has
  `<meta name="description">`, and the index has none. Add them.
- [ ] **P1 · Open Graph and Twitter cards.** None of the pages have OG tags.
  Add them, plus a default social image (`assets/og-default.png`), so
  shared links render previews.
- [ ] **P1 · Canonical URLs and JSON-LD** (`TechArticle`) on every article.
- [ ] **P1 · `sitemap.xml` and `robots.txt`.** Ideally generate both from
  `metadata.json` with a small script.
- [ ] **P1 · RSS/Atom feed** (`feed.xml`) generated from `metadata.json`.
  This audience reads with feed readers.
- [ ] **P2 · `404.html`** that matches the site's look, with a link back to
  the catalog.
- [ ] **P2 · Favicon** (inline SVG).

## 5. Content (P1)

- [ ] **P1 · Fill the pillar gaps.** Of the 12 published articles, 6 are
  about cost and governance. Reliability, security, and observability need
  more coverage. Candidate topics that match the README's catalog summary:
  - Structured output verification / schema-repair loop (reliability)
  - Idempotent tool execution and dry-run sandboxing (reliability, agents)
  - Guardrail interceptors: pre-flight sanitization and post-flight checks
    (security)
  - Prefix-cache-aligned prompts (context, cost)
  - Model routing and fallback cascades across providers (reliability)
  - Prompt and eval regression gates in CI (observability)
- [ ] **P1 · Write "Reading the Pattern Language"** (the draft that is
  currently missing). It explains how the catalog is organized, the five
  pillars, and how to go from a symptom to a pattern. It should be the one
  pinned "Start here" article.
- [ ] **P1 · Consolidate overlapping cost articles.** *Bleeding Tokens
  Silently*, *Predictive Token-Budget Circuit Breaker*, *The Infinite
  Wallet*, *The Fungibility Trap*, and *Your Budget Is a Receipt* cover
  similar ground. Add a cost-governance hub page, or "Hidden Connections"
  sections, that explain how they differ (where in the stack each one
  enforces).
- [ ] **P1 · Sources sections.** Most articles don't have an explicit
  Sources list. Add one to each article, respecting its research window.
- [ ] **P2 · Retrofit older articles** to the master-prompt structure
  (pattern card, checklist, recall practice) where sections are missing.

## 6. Consistency across articles (P2)

- [ ] **P2 · Shared design system.** Every article defines its own palette
  and fonts. Some match the index (navy and cyan, Space Grotesk/Plex);
  `deaf-at-the-wheel` uses a different palette and system fonts. Extract a
  `assets/site.css` with tokens and base layout, keep article-specific CSS
  inline, and migrate the articles gradually.
- [ ] **P2 · Uniform chrome.** Use the same top bar, back-to-catalog link
  (relative `../index.html`, not absolute URLs), TOC rail, progress bar, and
  footer on every article.
- [ ] **P2 · Accessibility pass.** Five articles have no `aria-*`
  attributes at all. Add labels to interactive widgets and SVGs, check
  contrast, and test keyboard navigation.
- [ ] **P2 · `<title>` convention.** Use `"<Title> — Production AI Patterns"`
  everywhere. Pages currently use `|`, `—`, or no suffix.

## 7. README and docs (P1)

- [ ] **P1 · Fix broken markdown.** The clone command contains
  `[url](url)` inside a code block, and the deployment link is a
  `google.com/url?...` redirect. Use plain URLs.
- [ ] **P1 · Add an "Articles" section** to the README that lists the
  published patterns (or links to the site), and an "Authoring" section
  that points to `docs/master-prompt-v1.md` and the metadata schema.
- [ ] **P2 · Align the README catalog summary** with what has actually been
  published, or mark the planned items as "coming".
- [ ] **P2 · `CONTRIBUTING.md`** with the article workflow: master prompt,
  then local review, then metadata, then PR.
