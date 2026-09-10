# Document Extraction Platform — Initial Epics and User Stories

This file captures the initial outcome-oriented work decomposition. It should be refined after the discovery session and representative-document review.

## Epic 1 — Product Discovery and Golden Dataset

**Outcome:** Understand the real Dad/Ambrook workflow and establish representative source documents and expected values before implementation architecture is locked.

### Story 1.1 — Requirements Discovery
As the product owner, I want a structured discovery session so the engineering team understands my actual workflow, downstream requirements, review expectations, and error tolerance.

### Story 1.2 — Representative Document Inventory
As the engineering team, we want a representative sample covering different institutions, years, native PDFs, scanned PDFs, good and poor scans, tables, multi-account statements, rotated/skewed pages, and duplicate/amended statements.

### Story 1.3 — Golden Dataset
As the engineering team, we want a manually verified dataset so extraction quality can be measured objectively using field accuracy, document success rate, false-confidence rate, human-review rate, processing time, and cost per document.

## Epic 2 — Document Intake
**Outcome:** Users can upload one or many PDFs and create durable processing jobs without keeping the browser session open.

Candidate stories include upload, stable document identity, job creation/status, corrupt-input handling, and practical duplicate detection.

## Epic 3 — Text and OCR Extraction
**Outcome:** The platform obtains usable machine-readable content from native and scanned documents while preserving page-level provenance.

## Epic 4 — Structured Extraction
**Outcome:** Users can define the fields they need from a document class and receive structured candidate values with source evidence.

## Epic 5 — Confidence and Validation
**Outcome:** The system prioritizes human attention by distinguishing trustworthy results from questionable ones using field-level confidence and deterministic validation where practical.

## Epic 6 — Provenance and Source Navigation
**Outcome:** A reviewer can move from any material extracted value directly to its supporting source PDF/page, with source region or snippet where practical.

## Epic 7 — Human Review
**Outcome:** Users can rapidly approve or correct uncertain results while viewing source evidence, without reviewing every high-confidence field.

## Epic 8 — Export
**Outcome:** Approved structured data can be transformed into downstream-compatible files, initially CSV and JSON, with Ambrook as the first concrete profile.

## Epic 9 — Pluggable Inference
**Outcome:** Users can choose local, cloud, or hybrid semantic inference providers based on privacy, cost, and speed.

Candidate providers include local OpenAI-compatible endpoints, OpenAI, Anthropic, Ollama/llama.cpp, and JARVIS.

## Epic 10 — Dad Historical Financial Records
**Outcome:** Recover historical financial values from Dad's investment statements and produce an Ambrook-importable dataset with source provenance.

### Initial acceptance scenario
Given a representative historical statement set, the system can ingest the documents, extract required quarterly values, surface uncertainty for review, accept corrections, and generate an importable dataset where each material value can be traced back to the originating document and page.

## Epic 11 — JARVIS Inference Integration
**Outcome:** The project owner's deployment can use JARVIS as the broker for primary local-LLM inference rather than calling llama.cpp directly.

Candidate stories include background inference submission, status/results, JARVIS-side queuing, yielding to interactive work, quiet-hours draining, and provider/job traceability.

This Epic is an integration feature. The public platform must remain usable without JARVIS.
