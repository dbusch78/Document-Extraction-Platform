# Document Extraction Platform

## Purpose

Turn PDFs and scanned documents into trustworthy structured data with field-level confidence, source provenance, fast human review, and exportable approved results.

The product should minimize manual transcription without pretending extraction is infallible.

## Core Experience

```text
Upload / scan documents
        ↓
Extract native text and/or OCR
        ↓
Identify document type
        ↓
Extract structured fields
        ↓
Attach confidence + provenance
        ↓
Review only uncertain/conflicting fields
        ↓
Approve / correct
        ↓
Export structured data
```

## Defining Product Principle

> **Every important extracted value remains traceable to the source document and page that produced it.**

If an extracted value has low confidence, the user should be able to open the original PDF at the relevant page, validate the source, correct the value if necessary, and continue without hunting for the document manually.

## What This Project Is

This is a local-first, provider-neutral document extraction and review platform.

It is designed to support:

- native PDF text extraction
- OCR for scanned or image-based documents
- structured field extraction
- field-level confidence
- source-document and page provenance
- human review and correction
- batch processing
- CSV and JSON export
- local, cloud, or hybrid inference providers

The platform is intended to be useful for personal, family, farm, and small-business document workflows without becoming a general-purpose enterprise document-management system.

## Initial Use Case

The first production use case is historical financial record reconstruction.

The goal is to process a multi-year backlog of investment and IRA statements, extract the statement date, account number, and a handful of summary values from each, map them to the right downstream account, review uncertain fields, and export the results into a format suitable for downstream import.

Ambrook is the first concrete downstream target, but Ambrook-specific mappings should remain an adapter or export profile rather than part of the core platform.

## Provider-Neutral Inference

The platform should not depend on a single AI provider.

Potential inference backends may include:

- OpenAI
- Anthropic
- local OpenAI-compatible endpoints
- llama.cpp
- Ollama
- JARVIS
- future custom HTTP or MCP providers

This allows a deployment to use local inference for privacy and low operating cost, cloud inference for large historical batches, or a hybrid approach where frontier models are used only for difficult or low-confidence cases.

## Human Review

The system should direct attention to uncertainty rather than forcing the user to validate every extracted value.

A review item should make it easy to see:

- what was extracted
- the confidence level
- why confidence may be low
- the source document
- the source page
- the supporting text or region where practical
- whether the value has been approved or corrected

The original machine-extracted value should remain available after a correction.

## Project Documentation

- [`Docs/PRD.md`](Docs/PRD.md) — product goals, requirements, scope, and first-release success criteria
- [`Docs/ARCHITECTURE.md`](Docs/ARCHITECTURE.md) — system boundaries, provider model, processing flow, storage, and review architecture
- [`Docs/USER-STORIES.md`](Docs/USER-STORIES.md) — initial Epics and candidate user stories
- [`Docs/GOLDEN-DATASET.md`](Docs/GOLDEN-DATASET.md) — format of the private golden dataset and document inventory; templates in `Docs/templates/`
- `Docs/private/` (gitignored) — owner-specific discovery material for the first real-world use case; a private deployment supplies its own

## Project Status

Requirements discovery for the first use case is complete. Current work is in the GitHub Project linked from this repository.
