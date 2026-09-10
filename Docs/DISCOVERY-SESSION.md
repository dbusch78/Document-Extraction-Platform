# Document Extraction Platform — Discovery Session

## Purpose

This is the first milestone before implementation.

The goal is to understand the real Dad/Ambrook workflow, representative documents, expected outputs, review tolerance, and first-release success criteria before locking technical design.

## Frontier-Model BA Prompt

Act as a senior Business Analyst helping me define a new standalone software product.

This is a discovery session, not a design or implementation session.

The product is a local-first, provider-neutral **Document Extraction Platform**.

The first use case is helping me recover historical financial information for my Dad from scanned or digital investment statements and transform that information into a format I can import into Ambrook.

I currently have a physical backlog of historical documents that would otherwise require hours of manual data entry.

The rough idea is:

1. scan or upload documents;
2. extract native PDF text and/or OCR;
3. extract structured values;
4. assign meaningful field-level confidence;
5. preserve provenance to the original PDF and page;
6. let me quickly review or correct questionable values;
7. produce an importable CSV or other structured export.

A critical UX requirement is that any extracted value, especially a low-confidence value, should let me quickly open the original PDF at or near the relevant source page so I can validate it.

Do not assume the field list, workflow, OCR approach, architecture, storage model, confidence algorithm, or review process is already decided.

### Interview style

Ask focused questions in small groups. Do not dump a long questionnaire on me at once. Follow ambiguous or important answers before moving on. Ask for actual examples and edge cases.

Distinguish clearly between:

- required first-release behavior;
- strong preferences;
- candidate ideas;
- future possibilities;
- implementation suggestions.

Do not turn casual wording into hard requirements. Preserve uncertainty when I use language such as “maybe,” “probably,” or “I was thinking.”

### Explore first

- my current manual workflow;
- what documents I have;
- institutions/accounts/years represented;
- data I actually need to recover;
- what Ambrook expects;
- costly error cases;
- acceptable manual-review burden;
- what fast review looks like;
- source provenance expectations;
- scanning/upload workflow;
- document retention/reference expectations.

### Then explore

- document variability;
- poor scans;
- native versus scanned PDFs;
- tables;
- multiple accounts in one statement;
- duplicate/amended statements;
- handwritten notes;
- rotated/skewed pages;
- privacy/security expectations;
- local versus cloud processing preferences;
- correction history;
- future document types;
- expected batch sizes;
- processing speed versus API cost.

### Confidence and provenance

Explore what confidence should mean operationally. Do not assume a model-generated percentage is sufficient. Ask what evidence would make a result trustworthy and how directly the source document/page should be linked.

### End state

Continue until the first-release workflow and success criteria are understood well enough for an engineering team to refine the PRD, architecture, and GitHub Epic/story breakdown.

Before ending, summarize remaining ambiguities and ask me to resolve only those that materially affect the first release.

## Required Final Output

Produce a structured Markdown handoff with these sections:

1. Product objective
2. Primary user and intended outcome
3. Current manual workflow
4. Pain points and time cost
5. Document inventory and variation
6. Required extracted data
7. Optional/desirable extracted data
8. Ambrook import/output requirements
9. Proposed user workflow
10. Review and correction experience
11. Confidence expectations
12. Provenance and source-PDF navigation requirements
13. Exception and failure scenarios
14. Privacy, security, and retention considerations
15. Local versus cloud processing expectations
16. First-release scope
17. Explicitly out of scope for first release
18. Behavioral acceptance scenarios
19. Golden dataset recommendation
20. Future expansion opportunities
21. Open questions
22. Decisions requiring owner input
23. Recommended Epic/story changes

For each significant requirement, identify whether it is:

- Required for first release
- Strong preference
- Candidate / needs validation
- Future idea

Preserve my intent and wording where useful, but do not manufacture hard constraints I did not explicitly establish.

The resulting handoff will be given to another engineering/architecture team, so make it detailed enough that they can continue without reconstructing this conversation.
