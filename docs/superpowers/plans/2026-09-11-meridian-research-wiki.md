# Meridian Stakeholder Interview Research Wiki Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a plain-GitHub-Markdown research wiki that turns supplied Meridian and course materials into a student-team-reviewed stakeholder briefing, interview guide, and traceable hypotheses.

**Architecture:** The repository will separate immutable supplied material in `raw/` from LLM-maintained content in `wiki/`. A root `AGENTS.md` will define ingestion, citation, review, and health-check rules; `wiki/index.md` and `wiki/log.md` will respectively provide navigation and an append-only record of changes. The initial implementation creates structure and templates only, because no Meridian source content is currently available.

**Tech Stack:** Git repository, plain GitHub-Flavored Markdown, relative Markdown links, PowerShell verification commands, Git history.

**Spec:** `docs/superpowers/specs/2026-09-11-meridian-research-wiki-design.md`

## Global Constraints

- Ingest only client- or course-supplied materials; do not use public-web or third-party sources.
- Treat `raw/` as immutable source-of-truth content; the LLM must never modify an existing raw source.
- Use standard relative Markdown links, not Obsidian `[[wikilinks]]` or YAML frontmatter.
- Place a direct raw-source link beside every factual claim once factual content is added.
- Clearly label each hypothesis and pair it with a validation question or evidence need.
- Student-team approval is required before LLM-proposed wiki updates are published.
- Faculty/advisor and Dana's team are reviewers, not editors.
- Do not invent Meridian-specific facts while the supplied brief remains empty.

---

## File structure

| Path | Responsibility |
| --- | --- |
| `AGENTS.md` | Canonical LLM operating schema and maintenance workflows. |
| `raw/` | Immutable client and course source material. Existing files remain unchanged. |
| `wiki/index.md` | Content dashboard and catalog. |
| `wiki/log.md` | Append-only history of ingests, approvals, queries, and health checks. |
| `wiki/briefing.md` | Meridian and stakeholder context, sourced facts, and open questions. |
| `wiki/interview-guide.md` | Prioritized interview questions, rationale, and expected evidence. |
| `wiki/hypotheses.md` | Labeled hypotheses, source links, validation prompts, and status updates. |
| `wiki/sources/README.md` | Rules and page template for one summary per raw source. |
| `wiki/entities/README.md` | Rules and page template for stakeholders, teams, organizations, and initiatives. |
| `wiki/concepts/README.md` | Rules and page template for domain terms and concepts. |
| `wiki/findings/README.md` | Rules and page template for post-interview synthesis. |

## Documentation validation convention

This implementation creates Markdown documents rather than executable software. Each task therefore uses structural checks instead of unit tests: inspect required headings, verify paths, search for disallowed link syntax, and run `git diff --check`. A reviewer must also open the Markdown files in a renderer or repository preview to confirm that relative links are usable.

---

### Task 1: Establish the LLM operating schema and protected source boundary

**Files:**
- Create: `AGENTS.md`
- Modify: none
- Preserve without modification: `raw/client-breif.md`

**Interfaces:**
- Consumes: the global constraints and the repository paths defined in the approved spec.
- Produces: the authoritative rules used by every later source-ingest, query, and health-check workflow.

**Done looks like:** `AGENTS.md` tells the LLM exactly where source and wiki content belong, who may publish changes, how claims and hypotheses are recorded, and how to perform ingest, query, and health checks. It explicitly forbids edits to existing files under `raw/`.

**How to check:** Read the file and confirm it contains sections named `Repository boundaries`, `Roles and approval`, `Citation and hypothesis rules`, `Ingest workflow`, `Query workflow`, and `Health check workflow`. Confirm `git diff -- raw/` is empty.

- [x] **Step 1: Write the schema file with repository and publishing rules.**

    Include these exact rules in `AGENTS.md`:

    ```markdown
    ## Repository boundaries

    - `raw/` contains client- and course-supplied source material. Never modify an existing file in `raw/`.
    - `wiki/` contains LLM-maintained Markdown. Use standard relative Markdown links.
    - Do not add public-web, third-party, or unsourced material.

    ## Roles and approval

    - The MSBA student team is the only group that edits and publishes wiki content.
    - Faculty/advisor and Dana's team review the wiki only.
    - Prepare proposed changes first. Publish only after student-team approval.
    ```

- [x] **Step 2: Add citation, hypothesis, and maintenance rules.**

    Add these operational requirements:

    ```markdown
    ## Citation and hypothesis rules

    - Put a direct relative link to the supporting raw source beside every factual claim.
    - Label every hypothesis as `Hypothesis` and include its status, evidence links, and validation question.
    - Record uncertainty and conflicting source material as an open validation question; do not resolve it without evidence.

    ## Ingest workflow

    1. Confirm a student-team member added a supplied source to `raw/`.
    2. Propose a source summary, affected-page changes, index entry, cross-links, and log entry.
    3. Wait for student-team approval.
    4. Publish accepted changes and append the log entry.
    ```

    Define `Query workflow` to search the index before reading linked pages, and `Health check workflow` to report broken links, contradictions, stale hypotheses, orphan pages, missing cross-links, and missing concept pages without publishing fixes automatically.

- [x] **Step 3: Verify the schema and source boundary.**

    Run:

    ```powershell
    $required = 'Repository boundaries','Roles and approval','Citation and hypothesis rules','Ingest workflow','Query workflow','Health check workflow'
    $text = Get-Content -Raw AGENTS.md
    $required | ForEach-Object { if ($text -notmatch [regex]::Escape($_)) { throw "Missing heading: $_" } }
    git diff -- raw/
    git diff --check
    ```

    Expected: no thrown error; no raw-source diff; no whitespace errors.

- [x] **Step 4: Commit the schema.**

    ```powershell
    git add AGENTS.md
    git commit -m "docs: add research wiki operating schema"
    ```

### Task 2: Create the wiki navigation and audit trail

**Files:**
- Create: `wiki/index.md`
- Create: `wiki/log.md`
- Modify: none

**Interfaces:**
- Consumes: the paths and workflow names defined by `AGENTS.md`.
- Produces: the entry point the LLM reads before answering a question and the chronological record every operation appends to.

**Done looks like:** The dashboard links to every core page and content area, explains the source and review boundaries, and displays empty-state guidance without making Meridian claims. The log defines a durable heading format for ingests, approved updates, queries, and health checks.

**How to check:** Render `wiki/index.md` and follow each relative link after Tasks 3 and 4 create its target. Confirm `wiki/log.md` includes a date-prefixed, append-only entry format and no non-template activity is recorded.

- [x] **Step 1: Create the index dashboard.**

    Use this initial structure in `wiki/index.md`:

    ```markdown
    # Meridian Research Wiki

    This wiki is maintained by the MSBA student team from supplied client and course materials. Changes are published only after student-team approval.

    ## Core preparation pages

    - [Meridian and stakeholder briefing](briefing.md)
    - [Interview guide](interview-guide.md)
    - [Hypotheses](hypotheses.md)

    ## Research catalog

    - [Source summaries](sources/README.md)
    - [Entities](entities/README.md)
    - [Concepts](concepts/README.md)
    - [Post-interview findings](findings/README.md)

    ## Wiki operations

    - [Activity log](log.md)

    ## Current state

    No Meridian source material has been synthesized yet. Add supplied material to `raw/`, then follow the ingest workflow in [`AGENTS.md`](../AGENTS.md).
    ```

- [x] **Step 2: Create the append-only log.**

    Use this initial structure in `wiki/log.md`:

    ```markdown
    # Meridian Research Wiki Log

    This file is append-only. Record each completed ingest, approved update, query output filed to the wiki, and health check.

    ## Entry format

    ## [YYYY-MM-DD] TYPE | Title

    - Status: proposed, approved, or completed
    - Source or affected pages: relative links
    - Summary: one concise sentence
    - Approval: student-team approver or `not applicable`
    ```

- [x] **Step 3: Verify navigation and log format.**

    Run:

    ```powershell
    rg -n '\]\((briefing|interview-guide|hypotheses|sources/README|entities/README|concepts/README|findings/README|log)\.md\)' wiki/index.md
    rg -n '^## \[YYYY-MM-DD\] TYPE \| Title$|append-only' wiki/log.md
    git diff --check
    ```

    Expected: the index command prints the eight listed destinations; the log command prints both the append-only statement and entry heading; no whitespace errors.

- [x] **Step 4: Commit the navigation and log.**

    ```powershell
    git add wiki/index.md wiki/log.md
    git commit -m "docs: add research wiki index and log"
    ```

### Task 3: Add the core interview-preparation page templates

**Files:**
- Create: `wiki/briefing.md`
- Create: `wiki/interview-guide.md`
- Create: `wiki/hypotheses.md`
- Modify: `wiki/index.md` only if relative-link verification identifies a path error

**Interfaces:**
- Consumes: the citation, hypothesis, and approval rules in `AGENTS.md` and links from `wiki/index.md`.
- Produces: the three core pages used to prepare for the interview.

**Done looks like:** Each core page is usable immediately as a template but contains no unsupported Meridian-specific content. The briefing has a claim-and-source pattern; the interview guide connects a question to rationale and expected evidence; and the hypotheses page requires label, status, evidence links, and validation question.

**How to check:** Confirm the exact heading sets below exist, search the pages for the literal `Meridian` only in template/context labels rather than factual prose, and verify all three relative links from the index resolve to files.

- [x] **Step 1: Create the sourced briefing template.**

    Create `wiki/briefing.md` with:

    ```markdown
    # Meridian and Stakeholder Briefing

    ## Sourced context

    Add confirmed context here. Every factual claim must link directly to its supporting file in `raw/`.

    ## Stakeholders and teams

    Add sourced stakeholder and team context here.

    ## Current initiatives and context

    Add only sourced initiatives and context here.

    ## Open questions

    - Add questions that supplied sources do not answer.
    ```

- [x] **Step 2: Create the interview-guide template.**

    Create `wiki/interview-guide.md` with:

    ```markdown
    # Meridian Stakeholder Interview Guide

    ## Priority questions

    ### [Priority] Question

    - Rationale: link to the relevant briefing claim or hypothesis.
    - Expected evidence: state what answer, artifact, or example would be useful.
    - Source context: link to the supporting raw source or write `No source context yet`.

    ## Follow-up questions

    Add conditional follow-ups that depend on the stakeholder's answer.
    ```

- [x] **Step 3: Create the hypothesis template.**

    Create `wiki/hypotheses.md` with:

    ```markdown
    # Meridian Research Hypotheses

    ## Hypotheses

    ### Hypothesis: [statement]

    - Status: proposed
    - Evidence: link to one or more raw sources.
    - Validation question: state the question that would confirm, change, or reject this hypothesis.
    - Latest update: no interview evidence yet.

    ## Resolved hypotheses

    Move a hypothesis here only after a sourced interview finding updates it in place.
    ```

- [x] **Step 4: Verify core templates.**

    Run:

    ```powershell
    $files = 'wiki/briefing.md','wiki/interview-guide.md','wiki/hypotheses.md'
    $files | ForEach-Object { if (-not (Test-Path $_)) { throw "Missing file: $_" } }
    rg -n '^## Sourced context$|^## Priority questions$|^### Hypothesis: \[statement\]$|^\- Status: proposed$|^\- Validation question:' wiki/briefing.md wiki/interview-guide.md wiki/hypotheses.md
    git diff --check
    ```

    Expected: all files exist; the command prints the briefing, guide, and hypothesis template markers; no whitespace errors.

- [x] **Step 5: Commit the core preparation templates.**

    ```powershell
    git add wiki/briefing.md wiki/interview-guide.md wiki/hypotheses.md
    git commit -m "docs: add interview preparation templates"
    ```

### Task 4: Add research catalog and post-interview templates

**Files:**
- Create: `wiki/sources/README.md`
- Create: `wiki/entities/README.md`
- Create: `wiki/concepts/README.md`
- Create: `wiki/findings/README.md`
- Modify: none

**Interfaces:**
- Consumes: the raw-source rule, direct-citation rule, and post-interview ingest workflow in `AGENTS.md`.
- Produces: documented homes and concrete page formats for generated source summaries, entities, concepts, and interview findings.

**Done looks like:** All catalog directories are represented in Git by one concise README that states its purpose and provides a reproducible page template. The findings template makes interview notes a raw source and requires explicit updates to related briefing and hypothesis pages.

**How to check:** Confirm all four README files exist, each includes a `## Page template` heading, and the findings template names interview notes as the source while requiring links to updated pages.

- [x] **Step 1: Create the source-summary catalog guide.**

    Create `wiki/sources/README.md` with a `## Purpose` section stating that every file added to `raw/` gets one generated summary page, and a `## Page template` section:

    ```markdown
    # [Source title]

    - Raw source: [link](../../raw/example.md)
    - Source type: client or course material
    - Ingest status: proposed or approved

    ## Key points

    ## Claims added or changed

    ## Related pages
    ```

- [x] **Step 2: Create entity and concept catalog guides.**

    Give `wiki/entities/README.md` this page template:

    ```markdown
    # [Entity name]

    ## Role and context

    ## Sourced facts

    ## Related sources

    ## Related hypotheses and questions
    ```

    Give `wiki/concepts/README.md` this page template:

    ```markdown
    # [Concept name]

    ## Definition in project context

    ## Sourced evidence

    ## Related entities, questions, and hypotheses
    ```

    Both files must state that factual text requires direct raw-source links.

- [x] **Step 3: Create the post-interview findings catalog guide.**

    Create `wiki/findings/README.md` with a page template that begins:

    ```markdown
    # [Interview date] Stakeholder Findings

    - Raw source: [interview notes](../../raw/interview-notes-YYYY-MM-DD.md)
    - Approval: student-team approver

    ## Confirmed or changed hypotheses

    ## New sourced findings

    ## Pages updated

    - [Briefing](../briefing.md)
    - [Hypotheses](../hypotheses.md)
    - [Interview guide](../interview-guide.md)
    ```

- [x] **Step 4: Verify catalog templates.**

    Run:

    ```powershell
    $files = 'wiki/sources/README.md','wiki/entities/README.md','wiki/concepts/README.md','wiki/findings/README.md'
    $files | ForEach-Object { if (-not (Test-Path $_)) { throw "Missing file: $_" } }
    rg -n '^## Page template$|Raw source:|Pages updated|factual text requires direct raw-source links' wiki/sources/README.md wiki/entities/README.md wiki/concepts/README.md wiki/findings/README.md
    git diff --check
    ```

    Expected: all files exist; each README exposes its template; findings includes `Raw source` and `Pages updated`; entity and concept guides state the citation rule; no whitespace errors.

- [x] **Step 5: Commit the research catalog templates.**

    ```powershell
    git add wiki/sources/README.md wiki/entities/README.md wiki/concepts/README.md wiki/findings/README.md
    git commit -m "docs: add research catalog templates"
    ```

### Task 5: Validate the initial wiki and record initialization

**Files:**
- Modify: `wiki/log.md`
- Verify: `AGENTS.md`, `wiki/index.md`, `wiki/briefing.md`, `wiki/interview-guide.md`, `wiki/hypotheses.md`, and every catalog README

**Interfaces:**
- Consumes: all structures and rules from Tasks 1 through 4.
- Produces: a validated, initialized wiki with an auditable record of its creation and no Meridian facts invented from the empty brief.

**Done looks like:** The repository has the complete planned structure, the index links to every target file, no Obsidian-only links or YAML frontmatter appear, all mandatory operational rules are present, and the log documents the initialization as a completed structural setup rather than a source ingest.

**How to check:** Run the complete command set below. Open `wiki/index.md` in a Markdown renderer and follow every link. Review the diff to confirm the initial wiki adds no factual claims about Meridian and does not alter `raw/`.

- [x] **Step 1: Append the initialization record to the log.**

    Append this completed entry to `wiki/log.md`:

    ```markdown
    ## [YYYY-MM-DD] initialization | Meridian Research Wiki

    - Status: completed
    - Source or affected pages: [index](index.md), [briefing](briefing.md), [interview guide](interview-guide.md), [hypotheses](hypotheses.md)
    - Summary: Created the approved wiki structure and templates; no Meridian source material was synthesized.
    - Approval: student-team approver
    ```

- [x] **Step 2: Run the complete structural validation.**

    Run:

    ```powershell
    $required = @(
      'AGENTS.md','wiki/index.md','wiki/log.md','wiki/briefing.md','wiki/interview-guide.md','wiki/hypotheses.md',
      'wiki/sources/README.md','wiki/entities/README.md','wiki/concepts/README.md','wiki/findings/README.md'
    )
    $required | ForEach-Object { if (-not (Test-Path $_)) { throw "Missing required file: $_" } }
    if (rg -n '\[\[|^---$' AGENTS.md wiki) { throw 'Found non-plain-Markdown wiki syntax' }
    rg -n '\]\((briefing|interview-guide|hypotheses|sources/README|entities/README|concepts/README|findings/README|log)\.md\)' wiki/index.md
    rg -n '^## \[YYYY-MM-DD\] initialization \| Meridian Research Wiki$|no Meridian source material was synthesized' wiki/log.md
    git diff --check
    git diff -- raw/
    ```

    Expected: all required paths exist; no command throws; the index lists every planned target; the initialization record appears; no whitespace errors; no raw-source changes.

- [x] **Step 3: Perform the human-readable review.**

    Open `wiki/index.md` in a Markdown renderer and select each link. Confirm that every link reaches an existing Markdown document and that the content is templates and workflow guidance only, not unsourced Meridian facts.

- [x] **Step 4: Commit the initialization record.**

    ```powershell
    git add wiki/log.md
    git commit -m "docs: record research wiki initialization"
    ```

## Spec coverage review

| Spec requirement | Implemented by |
| --- | --- |
| Immutable raw sources and generated wiki separation | Tasks 1 and 5 |
| Student-team publication approval and reviewer-only access | Task 1 |
| Dashboard and chronological log | Task 2 |
| Briefing, interview guide, and labeled hypotheses | Task 3 |
| Source, entity, concept, and post-interview finding pages | Task 4 |
| Claim-level source links and uncertainty handling | Tasks 1, 3, and 4 |
| Interview-note ingestion and synthesis | Tasks 1 and 4 |
| Periodic health check | Task 1 |
| No invented Meridian content before sources arrive | Tasks 1, 3, and 5 |

## Review notes: approved external-source ingests

### ICSC grocery formats, store growth, and investment activity

- **What I asked the AI to do and what came back:** I requested ingestion of the specifically designated ICSC article and later asked the AI to check one claim against the original article. The AI created the source record and source summary, added sector context to the briefing, interview guide, hypotheses, and grocery-formats concept page, linked the source from the index, and recorded the ingest in the log. For the checked claim, the AI reported that the article's investment section supported the wiki statement that it describes continuing investment activity in grocery-anchored retail centers.
- **How I checked the work and what I found:** I checked the ICSC source against the original article. I found that the article supported the claim the AI checked.
- **What I accepted, changed, or rejected and why:** I accepted the ingest because the checked claim matched the original source. No additional change or rejection was documented.

### SafeGraph retail site selection checklist

- **What I asked the AI to do and what came back:** I requested ingestion of the specifically designated SafeGraph article and asked to review the retrieved content before approval. The AI returned the article's ten-step site-selection checklist and its grocery-specific discussion of trade areas, parking, and competitive saturation. After approval, it created the source record, source summary, retail-site-selection concept page, related briefing, interview-guide, and hypothesis updates, index link, and log entry.
- **How I checked the work and what I found:** I opened the original SafeGraph article and reviewed the content the AI retrieved. I found that the retrieved content matched the original source.
- **What I accepted, changed, or rejected and why:** I accepted the ingest because the retrieved content matched the original article I reviewed. No additional change or rejection was documented.

### JLL Grocery Tracker 2026 — Value, Fresh Format & Location

- **What I asked the AI to do and what came back:** I requested ingestion of the specifically designated JLL article and asked to review the retrieved content before approval. The AI returned the article's discussion of value-focused and fresh-format grocery categories, overall grocery-opening concentrations in the Southeast and selected Mid-Atlantic markets, and the limitation that the geographic-opening data was not specialty-grocer-specific. After approval, it created the source record and source summary; updated the grocery-formats concept page, briefing, interview guide, hypotheses, index, and log; and revised the filed specialty-grocer-opening-locations analysis to retain that category limitation.
- **How I checked the work and what I found:** I opened the original JLL article and reviewed the content the AI retrieved. I found that the retrieved content matched the original source.
- **What I accepted, changed, or rejected and why:** I accepted the ingest because the retrieved content matched the original article I reviewed. No additional change or rejection was documented.
