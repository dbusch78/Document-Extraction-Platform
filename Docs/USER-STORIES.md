# Document Extraction Platform — Epics and User Stories

This decomposition follows the first-use-case workflow established in discovery: register a statement, identify its account, extract the summary values, review what is uncertain, export per downstream account, and find it again later. Live status lives in GitHub Issues and the GitHub Project, not here.

Owner-specific details (institutions, account numbers, mappings, sample values) stay in the owner's private discovery material.

## Milestone 0 — Product Discovery and Golden Dataset

**Outcome:** Understand the real first-use-case workflow and establish representative source documents and expected values before implementation architecture is locked.

Discovery is done. Remaining child work is the representative document inventory and the golden dataset.

## Milestone 1 — First Release: Investment Statement Backlog to Ambrook

### Epic — Document Intake and Statement Registration
**Outcome:** Users can upload one or many statement PDFs, native or scanned, and get durable processing jobs without keeping the browser open.

Candidate stories: upload digital PDF; register scanned PDF; preserve original filename and source file; extract basic statement metadata (institution, statement period); detect probable duplicate statements before export; tolerate minor rotation and scan imperfections; record processing status and failures.

### Epic — Account Identification and Mapping
**Outcome:** Every statement is tied to the correct downstream account, and nothing exports until it is.

Candidate stories: extract account number; maintain a configurable account-number-to-downstream-account/enterprise mapping; allow manual account assignment; block export while identity is unresolved; keep account title and institution as supporting, non-authoritative metadata.

This Epic carries the highest business importance: wrong-account assignment is the costliest error in the workflow.

### Epic — Statement Field Extraction
**Outcome:** The platform obtains usable text from native and scanned statements and produces the statement ending date and five summary values with source evidence.

Candidate stories: native text extraction with page provenance; OCR when native text is absent or unreliable; summary-page discovery via anchor labels rather than a fixed page; investment-statement extraction profile; zero-value detection; best-effort extraction on unfamiliar layouts with review flagging.

### Epic — Confidence and Review Queue
**Outcome:** Users see at a glance which statements and values need attention and can correct them quickly, without approving every high-confidence value.

Candidate stories: field-level confidence from deterministic signals plus model output; spreadsheet-style results grid; simple visual confidence cue; row drill-down with extracted text and confidence reasons; manual correction preserving the original value; configurable review thresholds; export eligibility without explicit approval for high-confidence values.

### Epic — Provenance, Source Navigation, and Reconciliation Lookup
**Outcome:** A reviewer can move from any material value to its source PDF page now, and find the statement again by account and period months later.

Candidate stories: associate each value with document and page; open the original PDF at or near the source page; display the extracted source text; search statements by account and period; reopen a processed statement and compare original and corrected values; show that a period is missing for an account; evaluate cropped source snippets as a fast-review enhancement.

### Epic — Export
**Outcome:** Export-eligible values become downstream-ready files, initially generic CSV and JSON plus an Ambrook profile.

Candidate stories: generic CSV/JSON export; Ambrook export profile confirmed against Ambrook's bulk-import format; one file per downstream account/enterprise; suppress zero-value rows; plain description convention; configurable field-to-category mapping per account or statement type; keep a link from each exported row to its extraction record; refuse export for unresolved-account statements.

### Epic — Inference Providers
**Outcome:** Users can choose local, cloud, or hybrid semantic inference based on privacy, cost, and speed, with no document content leaving the machine unless explicitly configured.

Candidate stories: provider interface; local OpenAI-compatible provider; at least one cloud provider (OpenAI or Anthropic); provider configuration and credential isolation; per-stage or per-job provider selection; frontier-model second opinion for low-confidence fields (candidate, pending the cloud-boundary decision).

### Validation gate
Before the full backlog is processed, extraction is measured against the golden dataset: field accuracy, account-identification accuracy (separately and with higher priority), false-confidence rate, human-review rate, duplicate detection, and regression across layout changes.

## Later

### Epic — JARVIS Inference Integration
**Outcome:** The project owner's deployment can use JARVIS as the broker for primary local-LLM inference rather than calling llama.cpp directly.

Candidate stories: background inference submission; status/results; JARVIS-side queuing; yielding to interactive work; quiet-hours draining; provider/job traceability. The public platform must remain usable without JARVIS.

### Deferred, pending owner decision
Multi-statement PDF splitting; bank-statement transaction extraction; 1099/tax-document extraction; correction-driven learning; automatic cloud escalation; advanced image cleanup; direct downstream API integrations; transaction-level investment extraction.
