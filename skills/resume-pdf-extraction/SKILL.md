---
name: "resume-pdf-extraction"
description: "Parses unstructured candidate PDF resumes into structured JSON models using PyMuPDF and LLM chunking."
license: MIT
---

# Resume PDF Extraction

## Overview
This skill extracts textual content and visual layout structures from uploaded candidate PDF resumes, transforming unstructured document text into strongly-typed Pydantic candidate models.

## Key Capabilities
- **High-Fidelity PDF Text Extraction**: Employs PyMuPDF and PyMuPDF4LLM to parse multi-column resume layouts without text jumbling.
- **Entity Identification**: Extracts candidate education history, degree titles, graduation years, GPAs, and certifications.
- **Work History Structuring**: Segments professional experiences with employer names, date ranges, and achievement bullet points.
- **Skill Taxonomy Mapping**: Normalizes declared technical skills, libraries, and frameworks into standard technical taxonomy terms.

## Operational Workflow
1. **Document Loading**: Ingest binary PDF stream and verify page readability.
2. **Layout Parsing**: Convert PDF pages into structured Markdown chunks via `resume_parser`.
3. **Structured Extraction**: Execute extraction prompts to populate candidate Pydantic data schemas.
4. **Validation**: Check for mandatory fields (education, skills) and sanitize any corrupted character encodings.
