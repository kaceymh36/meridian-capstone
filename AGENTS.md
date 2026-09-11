# Meridian Research Wiki Operating Schema

This schema governs the Meridian stakeholder-interview research wiki. Follow these rules for every source ingest, wiki update, query, and health check.

## Repository boundaries

- `raw/` contains client- and course-supplied source material. Never modify an existing file in `raw/`.
- `wiki/` contains LLM-maintained Markdown. Use standard relative Markdown links.
- Do not add public-web, third-party, or unsourced material unless the student team explicitly designates a specific external source for ingestion.
- For an explicitly designated external source, create an immutable source record in `raw/` that preserves its canonical URL, publisher, publication date when available, and ingest date. Do not treat that exception as permission to add other external sources.
- Keep the raw source layer separate from generated wiki content. Do not restate a raw document as the source of truth; link to it.

## Roles and approval

- The MSBA student team is the only group that edits and publishes wiki content.
- Faculty/advisor and Dana's team review the wiki only.
- Prepare proposed changes first. Publish only after student-team approval.
- Record the approval status for each completed source ingest or substantive update in `wiki/log.md`.

## Citation and hypothesis rules

- Put a direct relative link to the supporting raw source beside every factual claim.
- Label every hypothesis as `Hypothesis` and include its status, evidence links, and validation question.
- Record uncertainty and conflicting source material as an open validation question; do not resolve it without evidence.
- Do not state a hypothesis as a confirmed fact.
- When later evidence changes a hypothesis, update it in place, retain its original source links, and record the update in `wiki/log.md`.

## Ingest workflow

1. Confirm that a student-team member added a client- or course-supplied source to `raw/`, or explicitly designated a specific external source for ingestion.
2. Read the source without modifying it.
3. Propose a source summary, affected-page changes, index entry, cross-links, and log entry.
4. Wait for student-team approval.
5. Publish only the accepted changes in `wiki/` and append the completed action to `wiki/log.md`.

## Query workflow

1. Read `wiki/index.md` first to identify relevant pages.
2. Read the linked pages and their cited sources needed to answer the question.
3. Distinguish sourced facts, hypotheses, and open questions in the answer.
4. Cite the supporting raw-source links in the answer.
5. If the answer produces durable analysis, propose a new or updated wiki page for student-team approval before publishing it.

## Health check workflow

1. Check for broken or missing source links.
2. Check for contradictions, stale hypotheses, and unsupported factual claims.
3. Identify orphan pages, missing cross-links, and important concepts that lack a dedicated page.
4. Report recommended fixes and any research gaps to the student team.
5. Do not publish health-check fixes automatically; wait for student-team approval.
