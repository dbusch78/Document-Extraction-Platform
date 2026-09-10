# CLAUDE.md

## Project

This repository contains the **Document Extraction Platform**, a local-first, provider-neutral system for turning PDFs and scanned documents into trustworthy structured data with field-level confidence, source provenance, human review, and exportable approved results.

Read these files before making material product or architecture changes:

- `README.md`
- `Docs/PRD.md`
- `Docs/ARCHITECTURE.md`
- `Docs/USER-STORIES.md`
- `Docs/private/DISCOVERY-HANDOFF.md` (gitignored; present only in the owner's checkout)

If file paths differ, find the current equivalents rather than assuming the documentation is missing.

## Working Style

Use engineering judgment.

Do not turn preferences, examples, historical implementation choices, test details, or casual owner comments into permanent rules unless the repository clearly establishes them as such.

Do not add process, approval gates, governance language, abstractions, services, schemas, agents, or policy mechanisms merely to make the project look more formal.

Prefer the smallest design that satisfies the current product intent and preserves important trust, privacy, security, and data-integrity boundaries.

When a requirement or prior design appears stale, conflicting, or unnecessarily restrictive, call it out and propose the simpler/current interpretation rather than treating old text as untouchable authority.

## Authority and Strength of Statements

Interpret project direction using this hierarchy:

1. **Current explicit owner direction**
2. **Hard constraints**
3. **Product requirements**
4. **Architecture decisions**
5. **Design principles**
6. **Defaults**
7. **Preferences**
8. **Hypotheses / open questions**
9. **Historical notes**

Do not silently promote a lower-strength statement to a stronger category.

Examples:

- "The system must never send sensitive content to an external provider without explicit configuration" may be a real trust boundary.
- "Use Postgres for this table" may be an architecture choice.
- "Maybe use Redis" is only an idea.
- A test asserting an exact string is not automatically a product requirement.

## Owner Decisions

Escalate to the owner only when a genuine decision is needed.

Typical reasons include:

- a meaningful product choice with materially different user outcomes;
- privacy, security, or trust-boundary changes;
- authority to send, publish, delete, overwrite, or expose data;
- difficult-to-reverse data semantics;
- a real architecture conflict with significant consequences;
- meaningful recurring cost or external-provider exposure;
- requirements that cannot reasonably be inferred from existing product intent.

Do not request approval for routine implementation details, refactoring, test organization, naming, reversible technical choices, or ordinary engineering judgment.

When a decision is required, explain the issue plainly, present the meaningful options and consequences, and make a recommendation when appropriate.

## Product Boundary

The public platform owns generic document-processing capabilities such as:

- document ingestion;
- native text extraction and OCR;
- extraction profiles;
- structured field extraction;
- field-level confidence;
- provenance;
- source-PDF navigation;
- human review and correction;
- batch jobs;
- generic export;
- pluggable inference providers.

Private deployments may add private extraction profiles, downstream mappings, sensitive data, JARVIS integration, or personal context.

Do not bake the owner's private data or JARVIS-specific assumptions into the generic core.

## Inference Boundary

The platform is provider-neutral.

Potential providers may include OpenAI, Anthropic, local OpenAI-compatible endpoints, llama.cpp, Ollama, JARVIS, or future providers.

JARVIS is one provider/integration, not the core architecture.

For the owner's private deployment, local-model requests may flow through JARVIS so JARVIS can queue and prioritize access to the scarce local inference resource. Do not generalize that requirement to all deployments.

OCR and semantic inference are separate concerns.

## Provenance and Human Review

A defining product principle is:

> Every important extracted value remains traceable to the source document and page that produced it.

Machine extraction and human approval are distinct states.

Do not discard original extracted values when a human corrects them.

Low-confidence or conflicting values should be easy to review against the original source. Confidence should help prioritize attention and should not rely solely on a model's self-reported probability.

## GitHub Is the Live Planning System

This project should use GitHub Issues, native sub-issues, Milestones, and a GitHub Project as the live work-management system from the beginning.

Do not create parallel status documents, roadmap snapshots, kanban markdown files, or "current state" documents that duplicate GitHub.

Durable product rationale belongs in `PRD.md`, `ARCHITECTURE.md`, or other appropriate maintained documentation. Work status belongs in GitHub.

### GitHub Project

Use a GitHub Project for the repository.

Preferred project fields:

**Status**
- Triage
- Needs Decision
- Ready
- In Progress
- Blocked
- Done

**Delivery**
- leave empty while work is in flight
- Implemented
- Delivered

`Implemented` means built and validated in the repository.

`Delivered` means deployed, activated, released, or otherwise actually available to the intended user.

Do not use labels to duplicate these project fields.

### Issues and Hierarchy

Use:

- **Epic** issue: product outcome or substantial capability;
- **Story / task**: trackable work needed to achieve the Epic;
- **native sub-issues**: for genuine child work, not merely to mirror document headings;
- **Milestone**: a delivery increment containing work intended to ship or become available together.

Do not mechanically convert every PRD or design heading into an issue.

Create issues because the work needs to be tracked.

### Labels

Labels should describe stable type or area, not workflow state.

Useful initial labels may include:

- `epic`
- `feature`
- `bug`
- `documentation`
- `security`
- `discovery`
- `infrastructure`

Add domain labels only when they become useful.

Do not create labels such as `now`, `later`, `in-progress`, `blocked`, or `needs-decision` when those concepts already exist as Project fields.

### Needs Decision

Use `Needs Decision` when progress is genuinely blocked on owner judgment.

When moving an issue to `Needs Decision`:

1. add a concise new comment explaining the decision;
2. state the specific question;
3. provide enough context to decide without rereading the entire issue;
4. describe meaningful options and consequences;
5. provide a recommendation when appropriate.

After the owner decides:

1. move the issue out of `Needs Decision`;
2. update the issue body so the decision is reflected as current fact;
3. leave the comment history intact.

Do not use `Needs Decision` for routine implementation choices.

## GitHub Agent Identity

GitHub actions performed by the AI should use the repository's GitHub App identity when the required operation is supported.

The local command:

`gh-bot`

wraps the GitHub CLI with a fresh short-lived installation token for the `document-extraction-agent` GitHub App.

Use `gh-bot` instead of the owner's normal `gh` authentication for agent-authored repository activity such as:

- creating or editing issues;
- posting issue comments;
- creating or editing pull requests;
- posting pull-request comments;
- managing issue labels or milestones when supported;
- other repository operations that should visibly originate from the agent.

Do not expose, print, persist, log, or commit GitHub App tokens or the private key.

Do not read the GitHub App private key directly. Authentication details are encapsulated by `github-app-token` and `gh-bot`.

The owner's ordinary `gh` session remains available for operations the GitHub App cannot perform. Do not silently fall back to the owner's identity for authored comments, issue bodies, or pull-request discussion. If an operation that should carry agent attribution cannot be performed with `gh-bot`, report that limitation.

Git commits retain the normal repository development identity unless a separate commit-authorship approach is explicitly established.

### GitHub CLI

When authenticated and permitted, use `gh` for GitHub operations instead of asking the owner to perform repetitive manual steps.

Before making broad GitHub changes, inspect the current repository/project state.

Prefer idempotent or easily reviewable operations.

Do not delete issues, projects, milestones, labels, or history merely to make the workspace tidy unless that deletion is clearly intended.

## Development Approach

Work in small coherent slices.

Before coding:

1. understand the current issue/product outcome;
2. inspect the relevant code and documentation;
3. identify the simplest reasonable implementation;
4. note any genuine unresolved owner decision.

Then implement.

Do not produce speculative implementation plans that extend far beyond the current work.

Do not build generalized infrastructure for hypothetical future features unless the current design clearly needs the extension point.

## Testing

Tests protect intended behavior, trust boundaries, security properties, and important contracts.

They should not unnecessarily freeze incidental implementation details.

Prefer:

- unit tests for deterministic behavior;
- focused integration tests for boundaries;
- representative document fixtures;
- golden datasets for extraction quality;
- acceptance tests for important user workflows.

Avoid tests that merely assert exact prompt wording, incidental function structure, exact counts, timing minutiae, or other details that do not represent a real product or architecture requirement.

If a test conflicts with changed product intent, update the test rather than treating the test as authority.

## Documentation

Documentation should capture durable product intent, architecture, interfaces, important tradeoffs, and operational facts that need to survive implementation changes.

Do not write documentation as legalistic policy.

Avoid words such as "mandate", "ruling", "ratification", "governance approval", or "absolute" unless they genuinely describe the situation.

When documenting a decision, preserve its actual strength and rationale.

Historical material is context, not current authority, unless explicitly carried forward.

## Security

Treat documents as untrusted input.

Pay particular attention to:

- malicious or malformed files;
- path traversal;
- document-driven prompt injection;
- unsafe external links;
- provider credentials;
- local vs cloud trust boundaries;
- accidental external disclosure;
- browser access to sensitive source documents;
- export integrity;
- authentication and authorization;
- source-document integrity;
- auditability of human corrections.

Implement deterministic security boundaries in code where practical rather than relying on model obedience.

## Subagents

Use subagents when they improve focus or provide independent review.

Available agent roles are defined under `.claude/agents/`.

Typical use:

- `implementer` for a scoped implementation slice;
- `reviewer` for independent code/design review;
- `security` for trust-boundary and security review;
- `doc-writer` for maintained documentation;
- `project-steward` for GitHub Project / issue / milestone administration.

Do not delegate merely to create ceremony. Keep ownership of the overall task coherent.

## Completion

When finishing a work slice:

- run the proportionate relevant tests;
- update maintained documentation if durable behavior changed;
- update the GitHub issue/project state when appropriate;
- summarize what changed, validation performed, and any real remaining blocker.

If the owner says "wrap up", stop expanding scope and bring the current work to a clean stopping point.
