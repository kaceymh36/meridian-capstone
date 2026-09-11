# Meridian Stakeholder Interview Research Wiki — Design Spec

## Purpose

Create a persistent, source-backed research wiki that prepares the MSBA student team for a Meridian stakeholder interview and continues to synthesize knowledge after the interview. The wiki will provide a concise Meridian and stakeholder briefing, a prioritized interview guide, and traceable hypotheses.

The design follows the LLM wiki pattern: immutable raw sources, an LLM-maintained Markdown wiki, and an explicit schema that controls ingestion and maintenance. The wiki is a compounding research artifact rather than a collection of files retrieved afresh for each question.

## Goals

- Prepare the student team with a reliable organization and stakeholder briefing.
- Turn supplied materials into prioritized interview questions and evidence-seeking hypotheses.
- Preserve claim-level citations to the supplied source material.
- Allow the student team to approve every generated update before it becomes part of the wiki.
- Ingest interview notes after the meeting and update the synthesis in place.

## Non-goals

- Public-web research, third-party research, or unsourced content.
- A general project-management system, decision tracker, or deliverables repository.
- Direct editing by faculty/advisors or Dana's team.
- Per-task data-handling controls; those are governed by the separate data-handling checklist.

## Participants and permissions

| Group | Access | Responsibility |
| --- | --- | --- |
| MSBA student team | Maintains and publishes the wiki | Adds sources, reviews LLM-proposed changes, and approves updates |
| Faculty/advisor | Reviews only | Provides feedback and academic guidance |
| Dana's team | Reviews only | Validates stakeholder and organizational context as appropriate |

## Repository structure

```text
raw/                 Immutable client and course source material
wiki/
  index.md           Dashboard and content catalog
  log.md             Append-only chronology of ingests and updates
  briefing.md        Meridian and stakeholder context
  interview-guide.md Prioritized questions, rationale, and expected evidence
  hypotheses.md      Hypotheses, citations, and validation questions
  sources/           One generated summary page per source
  entities/          Stakeholder, team, organization, and initiative pages
  concepts/          Domain concepts and terminology
  findings/          Post-interview synthesized findings
AGENTS.md            Schema, conventions, and maintenance workflows
```

`raw/` is the source of truth and is never edited by the LLM. `wiki/` is generated and maintained by the LLM after student-team approval. `AGENTS.md` is the operating schema that instructs the LLM how to add, update, cross-link, cite, and check wiki content.

## Content model

### Landing page

`wiki/index.md` is the entry point. It links equally to the briefing, interview guide, hypotheses, source summaries, entity pages, concept pages, and post-interview findings. Each index entry includes a one-line description and, where relevant, the most recent update date.

### Briefing

`wiki/briefing.md` synthesizes sourced facts about Meridian, relevant stakeholders, current context, known initiatives, and open questions. All factual statements include a direct link to their supplied source.

### Interview guide

`wiki/interview-guide.md` organizes the most useful questions by theme and priority. Each question records its rationale, the source or hypothesis it addresses, and the evidence that would make the answer useful.

### Hypotheses

`wiki/hypotheses.md` contains explicitly labeled hypotheses rather than presenting them as fact. Every hypothesis includes:

- A statement and current status.
- Direct source links for the information motivating it.
- A validation question or evidence needed to assess it.
- A concise update when later interview evidence confirms, changes, or rejects it.

Hypotheses are updated in place while retaining their original source links. The chronology of changes is recorded in `wiki/log.md` and preserved by repository history.

### Source, entity, concept, and findings pages

- Each source in `raw/` has a corresponding generated summary in `wiki/sources/`.
- Entity pages capture recurring people, teams, organizations, and initiatives, linking to the briefing, relevant sources, and hypotheses.
- Concept pages define recurring domain language and connect it to sources and interview questions.
- Post-interview findings synthesize the interview notes, identify changes to existing hypotheses, and link to relevant pages.

## Citation and information rules

- Only client- or course-supplied material may be ingested as a source.
- Every factual claim in the wiki links directly to its supplied source.
- Hypotheses must be labeled as hypotheses and must not be written as confirmed facts.
- When source material conflicts or information is unclear, the wiki records the uncertainty and a corresponding validation question rather than resolving it without evidence.
- The wiki must avoid copying mutable status values into multiple pages; current status has one canonical location and other pages link to it.

## Workflows

### Ingest a supplied source

1. A student team member adds the supplied material to `raw/` without modifying it.
2. The LLM reads the source and prepares proposed updates: a source summary, affected page changes, cross-links, index entry, and log entry.
3. The student team reviews the proposal and decides what to publish.
4. After approval, the LLM writes the accepted wiki updates and appends the completed action to `wiki/log.md`.

### Prepare for the stakeholder interview

1. Review `wiki/index.md`, `briefing.md`, `interview-guide.md`, and `hypotheses.md`.
2. Prioritize questions whose answers would validate high-impact or uncertain hypotheses.
3. Verify that every factual claim supporting a question has a working source link.
4. Identify gaps or contradictions as explicit follow-up questions.

### Ingest the stakeholder interview

1. Add the interview notes to `raw/` as a new immutable source.
2. Generate a proposed interview-source summary and proposed updates to briefing, interview guide, hypotheses, entities, concepts, and findings.
3. Student team approves the proposed changes.
4. Update the affected pages, index, and log.

### Periodic health check

The LLM checks for broken or missing source links, contradictory claims, stale hypotheses, orphan pages, missing cross-references, and important concepts without dedicated pages. It reports recommended fixes for student-team approval; it does not publish them automatically.

## Acceptance criteria

- The repository clearly separates immutable supplied sources from generated wiki content.
- The dashboard exposes all briefing, interview, hypothesis, source, and findings content.
- Every factual claim can be traced to a supplied source.
- Every hypothesis has a validation question or evidence need.
- The student team reviews every ingest before wiki changes are published.
- Interview notes can be added as a source and used to update existing synthesis.
- The schema documents conventions for source ingestion, citations, updates, and health checks.

## Initial content constraint

At design time, the expected client brief was absent and the available likely file, `raw/client-breif.md`, was empty. The first implementation and ingest should therefore create the structure and schema without inventing Meridian-specific facts. Content is added only when supplied material becomes available.
