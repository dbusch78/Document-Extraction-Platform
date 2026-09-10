# Document Extraction Platform — Product Requirements Document

## 1. Product Vision

The Document Extraction Platform turns PDFs and scanned documents into trustworthy structured data with field-level confidence, source provenance, fast human review, and exportable approved results.

The platform should minimize manual transcription without pretending automated extraction is infallible.

The core product outcome is:

> Extract, verify where needed, and export structured data while keeping every important value traceable to its source.

The first production use case is historical financial record reconstruction for family financial administration, but the platform should remain broadly useful for personal, farm, and small-business documents.

## 2. Product Principles

- Local-first where practical.
- Provider-neutral for OCR and semantic inference.
- Human review focuses on uncertainty and exceptions, not on re-approving every value.
- Every material extracted value should retain provenance.
- Human-approved or corrected values must remain distinguishable from machine-extracted values.
- The costliest errors get deterministic blocking, not just a warning.
- The product should not become a generic enterprise document-management platform.
- Public/open-source functionality should remain generic; private downstream integrations, mappings, and sample documents live outside the public repository.

## 3. Initial Use Case

The first production use case is recovering a small set of summary values from a multi-year backlog of historical investment and IRA statements so they can be imported into Ambrook, a ledger product.

Today this requires opening each statement, finding the summary page, reading a handful of values, and keying each non-zero value into Ambrook as a separate ledger entry with the right account, description, category, and enterprise.

Discovery established the shape of this workflow (strength noted where it matters):

- **Documents.** Brokerage/investment and IRA statements, usually one statement per PDF, native or scanned. Layouts are repetitive within an institution but change over the years. The summary values usually appear on the first page, but the system must not assume that.
- **Required fields (required).** Statement ending date, account number, and five statement-level summary values: deposits, withdrawals, dividends/interest/other income, other transactions, and net change in portfolio value.
- **Supporting metadata (strong preference).** Institution, account title, statement period, source filename and page, and the extracted text the values came from. Mailing name and address are not reliable ownership signals; the account number is the identity key.
- **Account mapping (required).** The account number maps, through owner configuration, to a downstream account and enterprise. Assigning values to the wrong account is the costliest error in this workflow, far worse than a small amount error.
- **Export (required).** One export file per downstream account/enterprise, zero-value rows excluded, plain short descriptions.
- **Review (required).** A dense spreadsheet-style queue with drill-down to the source. Explicit per-field approval of high-confidence values is not required.
- **Reconciliation (required).** After downstream reconciliation reveals a discrepancy, the user can find the statement by account and date, see the original and corrected values, and open the source page.

Owner-specific details such as institutions, account numbers, mappings, and sample values live in the owner's private discovery material, not in this document.

Ambrook-specific column mappings are an export profile, not core platform behavior.

## 4. Target Users

Initial target user:

- A technically capable individual processing personal, family, farm, or small-business documents.

Potential future users include small farms, small businesses, bookkeepers, family financial administrators, people digitizing historical records, and users processing insurance, tax, medical, property, lease, or account documents.

## 5. Core Functional Requirements

The system should support PDF and batch upload, native PDF text extraction where available, OCR when needed, multi-page documents, reusable extraction profiles, structured field extraction, field-level confidence, source-document provenance, source page reference, source text or bounding region where available, configurable identity-to-downstream mapping, human review and correction, preservation of original and corrected values, probable-duplicate detection, resumable background processing, CSV and JSON export, provider-neutral OCR and inference, local-only operation, optional cloud/frontier inference, and auditability of machine extraction versus human approval.

## 6. Provenance Requirements

Every material extracted field should retain enough provenance to allow a reviewer to validate it quickly. The architecture should support document identifier, source filename, source page, source text or snippet, source bounding region where available, extracted value, extraction timestamp, extraction provider, confidence, review status, corrected value, and review timestamp.

A reviewer must be able to navigate from an extracted value to the source PDF at or near the relevant page. A later user should also be able to determine where an exported value came from, and find the statement again by account and date.

## 7. Confidence and Review

Confidence exists to prioritize human attention. The product should not rely solely on a language model self-reporting a confidence percentage.

Confidence may incorporate OCR quality, exact-value presence in extracted text, agreement between extraction passes, format validation, range validation, accounting or arithmetic consistency, expected-field presence, source-region clarity, conflicting candidate values, and model agreement where multiple providers are used.

The exact confidence algorithm and the thresholds that map confidence to review states are implementation decisions to be tuned against the golden dataset.

The review experience should minimize attention on high-confidence fields, surface low-confidence or conflicting values, explain important uncertainty where practical, provide immediate access to source evidence, and allow rapid correction and approval.

## 8. Review Experience

The primary review view is a dense grid: one row per statement, extracted values in columns, and a simple visual confidence cue (strong preference: green/yellow/red). Numeric confidence may appear in the detail view but should not dominate the grid.

Selecting a row opens the detail: statement identity, source filename, account number and its mapping, statement date, each extracted value with confidence and reason for uncertainty, the extracted source text, and actions such as View Source, Correct, and Approve.

Opening the source should bring the reviewer to the relevant PDF page and, where practical, identify or highlight the supporting region. A cropped snippet of the source field is a candidate enhancement.

## 9. Correction and Export Eligibility

A human correction becomes the approved value without destroying the original extraction result. The system should retain the original extracted value, corrected value, provenance, review timestamp, and review state.

Machine extraction and human approval remain distinct states, but export does not require explicit human approval of every value. A statement's values are eligible for export when:

- the account identity is resolved to a configured mapping (deterministic, no override by confidence); and
- each exported value is either high-confidence or has been approved or corrected by a human.

Statements with unresolved account identity are blocked from export until the user assigns or confirms the account. Exported rows retain a link to the extraction record they came from.

## 10. OCR and Document Text Extraction

OCR and semantic inference should remain separate concerns. Potential text/OCR providers may include native PDF text extraction, local OCR, cloud OCR, and future document-layout models. The pipeline should avoid OCR when reliable native PDF text already exists.

The first release expects reasonably good personally scanned documents. Rescue-grade OCR of severely degraded documents is out of scope.

## 11. Semantic Inference Providers

The product should support pluggable semantic inference providers, including OpenAI, Anthropic, local OpenAI-compatible endpoints, llama.cpp, Ollama, JARVIS inference service, and future custom HTTP or MCP providers.

Provider selection may eventually occur globally, per extraction profile, per job, or per processing stage. The core extraction pipeline should not depend on one provider.

## 12. Hybrid Inference

The architecture should support local-first processing, frontier-model fallback for uncertain cases, and deliberate cloud batch processing when speed is worth API cost.

Escalating low-confidence fields to a frontier model is a candidate, not a first-release requirement. Whether document content may leave the local environment at all, and under what conditions, is an open owner decision (see section 19). Until decided, no document content is sent to an external provider unless explicitly configured.

## 13. JARVIS Integration

In the project owner's deployment, local-model access must flow through JARVIS rather than directly to llama.cpp. JARVIS owns access to the scarce primary local inference resource and may schedule or queue document-extraction work around interactive workloads.

The public project should therefore support a JARVIS inference provider alongside direct local and cloud providers.

The public project owns documents, OCR/text extraction, extraction profiles, field-level confidence, provenance, review, corrections, export, and job state. JARVIS may own access to the primary local LLM, queuing/prioritization, resource scheduling, personal context, downstream reminders or commitments, and orchestration between private systems.

## 14. Exceptions

The first release should handle these situations deliberately:

- **Unresolved account identity.** Flag for review and block export (required).
- **Unfamiliar layout.** Attempt extraction anyway, flag uncertain results, allow correction. Do not reject the document (required).
- **Probable duplicate statement.** Detect before duplicate export rows are produced (required).
- **Missing statement.** Make it easy to see that a period is absent for an account so the user can locate the source (required).
- **Incorrect amount or category.** Tolerable if traceable and easy to correct after reconciliation (accepted operating condition).

## 15. Batch Processing

Large document batches should not require the browser session to remain open. Jobs should support queued, processing, awaiting review, completed, and failed states. Long-running jobs should be resumable where practical.

Typical batch sizes are not yet known and depend on the owner's scanning setup. Whether a single scan containing several statements must be split by the platform is an open owner decision.

## 16. Export

Core export formats are CSV and JSON. Export profiles may define column mappings, required fields, date formats, transformations, validation rules, and grouping (for example one file per downstream account). Ambrook is the first concrete export profile. Ambrook imports one CSV per account with interactive column mapping and a single signed amount column; whether category and enterprise can be imported or must be set in Ambrook afterwards is confirmed with a test import before the profile is finalized.

## 17. Privacy and Security

Documents may contain sensitive personal, financial, medical, or business information. The product should support local-only processing, make provider boundaries understandable, avoid silently sending document content to external providers, isolate provider credentials, preserve source-document integrity, retain audit information distinguishing machine extraction from human approval, and avoid exposing sensitive source documents unintentionally through the web UI.

## 18. Initial Out of Scope

The first release should avoid: trade-level or transaction-level extraction from investment statements, trade confirmations, handwritten records, bank-statement and tax-document extraction, legal or trust-accounting reasoning, automatic learning from corrections, fully automatic cloud escalation, rescue-grade OCR, guaranteed support for every layout, mandatory approval of every high-confidence value, enterprise document management, legal retention management, complex RBAC, generalized RAG/search across all documents, autonomous financial transactions, broad workflow automation unrelated to extraction, speculative document taxonomies, and a generic policy engine.

## 19. Open Owner Decisions

These materially affect implementation but do not block PRD or architecture refinement. Each is tracked on the relevant GitHub issue when it becomes blocking.

1. **Cloud-processing boundary.** Whether any document content may leave the local environment, and whether cloud escalation is automatic, opt-in per document, or globally configured.
2. **Multi-statement PDFs.** Whether the first release must split one scan containing several statements, or one statement per PDF is the operating procedure.
3. **Retention and storage.** Whether the platform copies source PDFs into managed storage or references them in place, and how long documents, OCR output, and extraction history are kept.
4. **Category treatment.** Account-specific category mappings for income and other activity need external validation before they are treated as authoritative. They stay configurable until then.
5. **Confidence thresholds.** Set after measuring against the golden dataset.
6. **Ending balance.** Discovery listed five flow values and no ending balance, while the goal includes tracking value over time. Confirm whether an extracted ending balance is wanted, at least as an arithmetic consistency signal.

## 20. First Release Success

The first release is successful when a representative set of historical investment and IRA statements can be ingested, extracted, mapped to the correct accounts, reviewed where uncertain, corrected where necessary, and exported into a usable Ambrook-compatible dataset with substantially less manual entry than transcription, while preserving source provenance for every material exported value.

Account-identification accuracy is measured separately from amount accuracy and is the higher-priority acceptance metric.
