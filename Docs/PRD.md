# Document Extraction Platform — Product Requirements Document

## 1. Product Vision

The Document Extraction Platform turns PDFs and scanned documents into trustworthy structured data with field-level confidence, source provenance, fast human review, and exportable approved results.

The platform should minimize manual transcription without pretending automated extraction is infallible.

The core product outcome is:

> Extract, verify, approve, and export structured data while keeping every important value traceable to its source.

The first production use case is historical financial record reconstruction for family financial administration, but the platform should remain broadly useful for personal, farm, and small-business documents.

## 2. Product Principles

- Local-first where practical.
- Provider-neutral for OCR and semantic inference.
- Human review focuses on uncertainty and exceptions.
- Every material extracted value should retain provenance.
- Approved/corrected values must remain distinguishable from machine-extracted values.
- The product should not become a generic enterprise document-management platform.
- Public/open-source functionality should remain generic; private downstream integrations may live outside the public repository.

## 3. Initial Use Case

The first production use case is extracting historical investment-account information from old statements so quarterly balances and related values can be imported into Ambrook.

Today this requires manually reading statements and keying values into another system.

The platform should:

1. ingest scanned or digital PDFs;
2. obtain usable text through native extraction and/or OCR;
3. identify relevant fields;
4. extract structured values;
5. assign meaningful field-level confidence;
6. preserve source document/page provenance;
7. surface uncertain or conflicting values for review;
8. allow fast correction and approval;
9. export approved data to CSV or another supported downstream format.

Ambrook-specific mappings should be implemented as an adapter or export profile rather than being hard-coded into the core platform.

## 4. Target Users

Initial target user:

- A technically capable individual processing personal, family, farm, or small-business documents.

Potential future users include:

- small farms;
- small businesses;
- bookkeepers;
- family financial administrators;
- people digitizing historical records;
- users processing insurance, tax, medical, property, lease, or account documents.

## 5. Core Functional Requirements

The system should support PDF and batch upload, native PDF text extraction where available, OCR when needed, multi-page documents, reusable extraction profiles, structured field extraction, field-level confidence, source-document provenance, source page reference, source text or bounding region where available, human review and correction, preservation of original and corrected values, resumable background processing, CSV and JSON export, provider-neutral OCR and inference, local-only operation, optional cloud/frontier inference, and auditability of machine extraction versus human approval.

## 6. Provenance Requirements

Every material extracted field should retain enough provenance to allow a reviewer to validate it quickly. The architecture should support document identifier, source filename, source page, source text or snippet, source bounding region where available, extracted value, extraction timestamp, extraction provider, confidence, review status, corrected value, and review timestamp.

A reviewer must be able to navigate from an extracted value to the source PDF quickly. A later user should also be able to determine where an exported value came from.

## 7. Confidence and Review

Confidence exists to prioritize human attention. The product should not rely solely on a language model self-reporting a confidence percentage.

Confidence may incorporate OCR quality, exact-value presence in extracted text, agreement between extraction passes, format validation, range validation, accounting or arithmetic consistency, expected-field presence, source-region clarity, conflicting candidate values, and model agreement where multiple providers are used.

The exact confidence algorithm is an implementation decision.

The review experience should minimize attention on high-confidence fields, surface low-confidence or conflicting values, explain important uncertainty where practical, provide immediate access to source evidence, and allow rapid correction and approval.

## 8. Review Experience

A representative review item may contain field name, extracted value, confidence, reason for uncertainty, source filename, page number, source text or region, and actions such as View Source, Correct, and Approve.

Opening the source should bring the reviewer to the relevant PDF page and, where practical, identify or highlight the supporting region.

## 9. Correction Behavior

A human correction becomes the approved value without destroying the original extraction result. The system should retain the original extracted value, corrected value, provenance, review timestamp, and review state.

## 10. OCR and Document Text Extraction

OCR and semantic inference should remain separate concerns. Potential text/OCR providers may include native PDF text extraction, local OCR, cloud OCR, and future document-layout models. The pipeline should avoid OCR when reliable native PDF text already exists.

## 11. Semantic Inference Providers

The product should support pluggable semantic inference providers, including OpenAI, Anthropic, local OpenAI-compatible endpoints, llama.cpp, Ollama, JARVIS inference service, and future custom HTTP or MCP providers.

Provider selection may eventually occur globally, per extraction profile, per job, or per processing stage. The core extraction pipeline should not depend on one provider.

## 12. Hybrid Inference

The architecture should support local-first processing, frontier-model fallback for uncertain cases, and deliberate cloud batch processing when speed is worth API cost.

## 13. JARVIS Integration

In the project owner's deployment, local-model access must flow through JARVIS rather than directly to llama.cpp. JARVIS owns access to the scarce primary local inference resource and may schedule or queue document-extraction work around interactive workloads.

The public project should therefore support a JARVIS inference provider alongside direct local and cloud providers.

The public project owns documents, OCR/text extraction, extraction profiles, field-level confidence, provenance, review, corrections, export, and job state.

JARVIS may own access to the primary local LLM, queuing/prioritization, resource scheduling, personal context, downstream reminders or commitments, and orchestration between private systems.

## 14. Batch Processing

Large document batches should not require the browser session to remain open. Jobs should support queued, processing, awaiting review, completed, and failed states. Long-running jobs should be resumable where practical.

## 15. Export

Core export formats are CSV and JSON. Adapters may define column mappings, required fields, date formats, transformations, and validation rules. Ambrook is the first concrete export target.

## 16. Privacy and Security

Documents may contain sensitive personal, financial, medical, or business information. The product should support local-only processing, make provider boundaries understandable, avoid silently sending document content to external providers, isolate provider credentials, preserve source-document integrity, retain audit information distinguishing machine extraction from human approval, and avoid exposing sensitive source documents unintentionally through the web UI.

## 17. Initial Out of Scope

The first release should avoid prematurely building enterprise document management, legal retention management, complex RBAC, generalized RAG/search across all documents, autonomous financial transactions, broad workflow automation unrelated to extraction, speculative document taxonomies, or a generic policy engine.

## 18. First Release Success

The first release is successful when a representative set of historical financial statements can be ingested, extracted, reviewed, corrected where necessary, and exported into a usable Ambrook-compatible dataset while preserving source provenance for every material exported value.
