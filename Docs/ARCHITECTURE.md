# Document Extraction Platform — Architecture

## 1. Architecture Goals

The architecture supports a local-first, provider-neutral document extraction and review platform without coupling the core product to one OCR engine, language model, downstream export target, or JARVIS.

It should remain simple enough to support the first use case (historical investment statements exported to Ambrook) while leaving clear extension points for future document types and inference providers.

## 2. High-Level Architecture

```text
                    Web UI
                       |
                       v
                 Application API
                       |
       +---------------+----------------+
       |               |                |
       v               v                v
 Document Store    Job Manager      Review Store
       |               |
       +-------+-------+
               |
               v
     Extraction Pipeline / Workers
               |
      +--------+---------+
      |                  |
      v                  v
Text / OCR         Inference Provider
                         |
          +--------------+----------------+
          |              |                |
          v              v                v
        Local          OpenAI         Anthropic
          |
          v
   JARVIS Provider
          |
          v
 JARVIS Orchestrator
          |
          v
 Primary Local LLM

               |
               v
        Structured Records
               |
               v
          Export Adapter
               |
               v
           CSV / JSON
```

## 3. Frontend

React / Next.js is the preferred direction. The UI is a core part of the product because review quality and source verification are as important as extraction.

Likely views include Jobs, Documents, Extraction Results, Review Queue, Source PDF Viewer, Extraction Profiles, Providers, Exports, and Processing History.

## 4. Application API

The API should provide a stable boundary between the UI and backend processing. Likely responsibilities include document upload, metadata, job creation/status, extraction results, review/correction, export, and provider configuration.

## 5. Job and Worker Model

Document processing should be asynchronous.

```text
ingest
→ detect native text
→ OCR if needed
→ classify document if useful
→ extract structured fields
→ validate
→ calculate confidence
→ persist results
→ queue uncertain fields for review
```

The queue/worker technology is intentionally not prescribed yet.

## 6. Persistence

Persistence should conceptually support document metadata, processing jobs, extraction profiles, extracted fields, provenance, review/corrections, export runs, and provider configuration. These concepts do not need to become separate services.

## 7. Document Storage

The system should support a document-storage abstraction, such as local filesystem, mounted NAS/share, or object storage. The database should retain stable identifiers and references to source files.

## 8. Extraction Profiles

An extraction profile defines what structured information is wanted from a document class. Profiles may define expected fields, field types, validation, extraction hints, required versus optional values, and export mappings without coupling to one model-provider prompt format.

The first profile is an investment/IRA statement summary: statement ending date, account number, and five summary amounts. Profiles should describe the fields and how to find the summary page (anchor labels), not one institution's layout, because layouts change across years.

## 8a. Account Identity and Mapping

Documents carry an identity key, for the first use case the account number, which the platform resolves against owner-configured mappings to downstream targets (for Ambrook: account and enterprise). The mapping table is configuration in the private deployment, not code.

Identity resolution is deterministic: an unresolved or ambiguous identity blocks export regardless of how confident the amount extraction is. This is the one place the design deliberately prefers a hard stop over a warning, because a wrong-account assignment is the costliest error downstream.

## 9. OCR/Text Provider Interface

OCR/text extraction should be separate from semantic inference.

```text
extract_text(document, page) -> text + layout/provenance
```

Implementations may include native PDF extraction, local OCR, cloud OCR, and future layout-aware extractors.

## 10. Semantic Inference Provider Interface

The semantic interface should remain provider-neutral.

```text
extract(schema, content, context) -> structured result
```

Potential providers include local OpenAI-compatible endpoints, llama.cpp, Ollama, OpenAI, Anthropic, JARVIS, and future MCP/custom providers.

## 11. JARVIS Provider

The JARVIS provider is specific to deployments where JARVIS owns access to the local model.

```text
Document Platform
      |
      v
JARVIS inference request
      |
      v
JARVIS queue / scheduler
      |
      v
Primary local LLM
```

The document platform should treat JARVIS as an inference provider, not as part of its core architecture. JARVIS may delay, prioritize, or batch requests to maximize limited local GPU capacity.

## 12. Confidence Layer

Confidence should be computed by the platform rather than delegated entirely to the LLM. Signals may include OCR confidence, validation results, source-text match, repeated-pass agreement, accounting consistency, provider disagreement, and document quality.

## 13. Provenance Model

Every material extracted field should be traceable to source evidence, including document, page, source text, source bounding region where available, extraction pass/provider, timestamp, and validation evidence.

## 14. Review Model

Machine extraction and human approval are separate states, but review is exception handling rather than a gate on every value.

```text
extracted (high confidence)      → export-eligible
extracted (needs review)         → approved            → export-eligible
extracted (needs review)         → corrected → approved → export-eligible
```

Each value records whether it was machine-extracted, approved as-is, or corrected. The original machine-extracted value remains available after correction.

Export eligibility for a statement additionally requires resolved account identity (section 8a) and no probable-duplicate flag left unresolved.

Results are kept after export so the user can later search by account and period, reopen a statement, compare original and corrected values, and open the source page when downstream reconciliation reveals a discrepancy.

## 15. Export Layer

Export should occur from export-eligible structured records (section 14). Export adapters own downstream-specific transformations, including row filtering (for example dropping zero-value rows), grouping (for example one file per downstream account), descriptions, and column formats. Initial adapters are generic CSV, generic JSON, and an Ambrook-compatible export profile.

Each exported row keeps a reference to the extraction record it came from so an exported value can be traced back to its source page.

## 16. Public vs Private Boundary

The public repository should contain generic product capabilities. Private deployments may supply private extraction profiles, sensitive document samples, private downstream mappings, JARVIS credentials/configuration, and personal/family context.

No private data should be required to run or test the public project.

## 17. Security Architecture

Security design should consider provider credentials, document sensitivity, local versus cloud processing, browser access to source documents, file path traversal, malicious documents, prompt injection inside document text, unsafe external links, export integrity, correction auditability, and API authentication appropriate to deployment.

The semantic model should not be trusted to enforce security boundaries that can be implemented deterministically.

## 18. Initial Architectural Non-Goals

Do not prematurely add microservices solely for conceptual separation, a generic workflow engine, policy engines, enterprise RBAC, generalized document-search/RAG, multiple databases without demonstrated need, speculative event buses, or provider-specific assumptions in the core domain model.

## 19. Golden Dataset

Extraction and confidence behavior are validated against a small golden dataset of real statements with manually verified expected values. The dataset contains private financial documents and lives outside the public repository (in the owner's gitignored `Docs/private/` folder or another private location). The public repository documents the dataset format and may carry a few synthetic or fully redacted fixtures for automated tests.

Field accuracy and account-identification accuracy are measured separately; the latter is the higher-priority acceptance metric.
