# EXPLAINABILITY.md

This document explains the internal mechanisms, data lineage, operational boundaries, and governance framework of **Hiring Agent** (`hiring-agent`) in accordance with the **OpenGAP v0.1.0** specification for the **HiDevs GitAgent Passport** clearance pipeline.

> **Agent Name:** Hiring Agent (`hiring-agent`)  
> **Specification:** OpenGAP v0.1.0  
> **Category / Domain:** Human Resources / Candidate Resume Parsing, GitHub Signal Enrichment & Explainable Evaluation  
> **Compliance Standard:** OpenGAP Checkpoint 2 (Explainability & Decision Governance), OWASP LLM Top 10, MITRE ATLAS  

---

## How the Agent Decides

The Hiring Agent operates an objective, deterministic resume evaluation and candidate ranking pipeline designed to process high-volume application funnels fairly and transparently. Applications proceed through five sequential stages:

### 1. Decision Architecture

The runtime intake, state classification, evaluation, and execution tracking operate across a deterministic, five-stage pipeline:

```
[ Candidate Application Ingestion ]
                 │
                 ▼
[ 1. Document Extraction & PII Scrubbing ]
                 │
                 ▼
[ 2. Public GitHub Signal Enrichment ]
                 │
                 ▼
[ 3. Role-Fit Rubric Matching ]
                 │
                 ▼
[ 4. Composite Scoring & Cutoff Evaluation ]
                 │
                 ▼
[ 5. Recruiter Dossier Compilation ]
```

### 2. Decision Logic & Routing Formulations

When scoring a candidate against target role requirements, the pipeline evaluates a composite candidate score $S_{\text{composite}} \in [0, 100]$ across four explicit dimensions:

$$S_{\text{composite}} = w_{\text{edu}} \cdot S_{\text{edu}} + w_{\text{exp}} \cdot S_{\text{exp}} + w_{\text{skills}} \cdot S_{\text{skills}} + w_{\text{gh}} \cdot S_{\text{github}}$$

Where:
- $S_{\text{edu}} \in [0, 100]$: Alignment of degree level, graduation timeframe, and relevant coursework with role prerequisites.
- $S_{\text{exp}} \in [0, 100]$: Relevance, duration, and technical depth of past engineering internships or professional roles.
- $S_{\text{skills}} \in [0, 100]$: Coverage and proficiency across required and preferred programming languages and frameworks.
- $S_{\text{github}} \in [0, 100]$: Practical engineering signal derived from original public repositories, commit velocity, and language diversity.
- Default weighting coefficients: $w_{\text{edu}} = 0.20$, $w_{\text{exp}} = 0.30$, $w_{\text{skills}} = 0.30$, $w_{\text{gh}} = 0.20$ ($\sum w_i = 1.0$).

A candidate is flagged as `pass_to_human_review` if $S_{\text{composite}} \ge \tau_{\text{cutoff}} = 25.0$. The cutoff is intentionally set low so that only completely non-responsive or blank applications are removed, while all legitimate applicants pass through to human recruiters with rank ordering.

### 3. Thresholding & Refusal Decision Criteria

Hiring Agent enforces strict operational boundaries and deterministic refusal thresholds:
- **Refusal on ERR_BELOW_BASELINE_CUTOFF**: Candidate composite score falls below baseline cutoff ($S_{\text{composite}} < 25.0$) halts execution with code `ERR_BELOW_BASELINE_CUTOFF`.
- **Refusal on ERR_UNPARSEABLE_PDF**: Uploaded resume PDF is corrupted, password-protected, or unreadable halts execution with code `ERR_UNPARSEABLE_PDF`.
- **Refusal on ERR_GITHUB_RATE_LIMIT**: GitHub REST API rate limit reached or token invalid halts execution with code `ERR_GITHUB_RATE_LIMIT`.
- **Refusal on ERR_MISSING_MANDATORY_CRITERIA**: Candidate application lacks mandatory contact information or graduation date halts execution with code `ERR_MISSING_MANDATORY_CRITERIA`.
- **Refusal on ERR_UNSUBSTANTIATED_SCORE**: Scoring dimension lacks corresponding text evidence in resume or GitHub halts execution with code `ERR_UNSUBSTANTIATED_SCORE`.

### 4. Fallback Decision Mechanism

Continuous operational stability is maintained through layered fault recovery:
- **GitHub Signal Fallback**: If a candidate does not provide a GitHub handle or their profile has zero public activity, the GitHub weight $w_{\text{gh}}$ is redistributed proportionally across the remaining dimensions ($w_{\text{edu}} = 0.25, w_{\text{exp}} = 0.375, w_{\text{skills}} = 0.375$), ensuring candidates without public GitHub accounts are not penalized.
- **Model Provider Failover**: If the local Ollama inference engine (`gemma4:latest`) is unavailable, the pipeline falls back to cloud LLM providers (Google Gemini or OpenAI) via configured providers.
- **OCR Fallback**: If standard PyMuPDF text extraction yields insufficient text (< 100 characters), the pipeline routes the document through PyMuPDF4LLM layout parsing and OCR.
- **Model Fallback Cascade**: High-level reasoning and synthesis default to `gemini-2.0-flash` with automatic failover to `gpt-4o` and `claude-3-5-sonnet`.

### 5. Human-in-the-Loop Governance

Human operators retain sovereign authority over the multi-agent execution lifecycle:
- **Recruiter Priority Queue**: The agent solely orders and ranks resumes; all final decisions (shortlisting for interviews, scheduling, rejections) are executed by human recruiters.
- **Auditable Dossiers**: Each candidate record includes an explainable Markdown dossier highlighting top strengths, potential gaps, and suggested interview questions for the hiring manager.
- **Recruitment Campaign Calibration**: Hiring managers configure role rubrics and approve cutoff score thresholds prior to campaign launch.

---

## The Data It Uses

Hiring Agent operates under strict principles of data minimization, environment isolation, and privacy protection.

### 1. Ingested Input Data

The framework processes only operational data necessary to perform its functions:
- **Resume Documents**: Unstructured candidate resume files in PDF format containing employment history, educational background, and technical projects.
- **GitHub Public Data**: Public repository metadata, primary languages, commit activity calendars, and star counts fetched via the GitHub API.
- **Job Role Specifications**: Structured Markdown and JSON role definitions detailing required qualifications, preferred tech stacks, and evaluation rubrics.

### 2. Configuration & Reference Data

- **Technical Skill Taxonomy**: Standardized technical ontology mapping synonyms, libraries, and frameworks (e.g., PyTorch $\to$ Deep Learning).
- **Role Rubric Schemas**: Weight vectors and point allocation rubrics for target job titles (e.g., Software Engineering Intern).
- **Cutoff Configurations**: Baseline score thresholds ($S_{\text{cutoff}}$) calibrated per recruiting campaign.

### 3. Base Model & Inference Lineage

- **Inference Backends**: Local Ollama runtime (`gemma4:latest`) for private offline evaluation, or cloud endpoints (Gemini 1.5 Pro/Flash, GPT-4o).
- **Parsing Stack**: PyMuPDF (`fitz`) and `pymupdf4llm` for document layout analysis and markdown generation.
- **Data Validation**: Strict Pydantic models ensuring strongly-typed entity extraction and JSON export integrity.

### 4. Data Privacy, Storage, and Retention

- **OWASP LLM & MITRE ATLAS Hardened**: Defended against indirect prompt injection, credential leakage, and unauthorized external API dispatch.
- **Local Environment Isolation**: Agent execution workspaces, intermediate scratchpads, and vector stores reside strictly within designated local project directories.
- **Automated Secret Scrubbing**: API keys, database credentials, and personal credentials are automatically redacted prior to embedding or logging.
- **Zero Commercial Monetization**: Prompts, intermediate reasoning trajectories, and task deliverables are never commercialized or shared with third parties.

---

## Limitations

Understanding the operational boundaries and technical constraints of Hiring Agent is essential for effective deployment.

### 1. Self-Reported Resume Claims
- **Limitation**: Resumes inherently contain self-reported claims that cannot be fully verified prior to technical interviews.
- **Mitigation**: Cross-reference claimed technologies with verifiable public GitHub contributions and generate targeted technical validation questions for interviewers.

### 2. Bias Toward Candidates with Public GitHub Activity
- **Limitation**: Candidates with extensive proprietary industry experience or non-public work may lack active public GitHub profiles.
- **Mitigation**: Automatically redistribute GitHub dimension weights to experience and skills when no GitHub profile is present, ensuring zero score penalty.

### 3. Non-Standard Resume Layout Parsing
- **Limitation**: Highly creative multi-column graphic resumes or infographic formats can occasionally disrupt reading order.
- **Mitigation**: Utilize PyMuPDF4LLM's layout-aware structural chunking to preserve block associations across complex layouts.

### 4. LLM Scoring Variability
- **Limitation**: Stochastic foundation models can produce minor score fluctuations across identical resumes if temperature is non-zero.
- **Mitigation**: Set model temperature to $0.0$ and enforce rigid rubric scoring guidelines with discrete point categories.

### 5. Non-English Resume Translation
- **Limitation**: Technical resumes written in languages other than English may experience slight terminology loss during parsing.
- **Mitigation**: Pre-translate non-English sections using multilingual embedding models before applying English role rubrics.

---

## Summary & Compliance Checklist

| Checkpoint 2 Requirement | Corresponding Section | Status |
| :--- | :--- | :---: |
| **How the agent decides** | [How the Agent Decides](#how-the-agent-decides) | **Covered** |
| - Decision architecture & 5-stage pipeline | Section 1 | Verified |
| - Decision logic & routing formulations | Section 2 | Verified |
| - Thresholding & refusal decision criteria | Section 3 | Verified |
| - Fallback decision mechanism | Section 4 | Verified |
| - Human-in-the-loop governance & oversight | Section 5 | Verified |
| **The data it uses** | [The Data It Uses](#the-data-it-uses) | **Covered** |
| - Ingested input data & query streams | Section 1 | Verified |
| - Configuration & reference schemas | Section 2 | Verified |
| - Base model lineage & deterministic engines | Section 3 | Verified |
| - Data privacy, retention lifecycle & MITRE/OWASP | Section 4 | Verified |
| **Its limitations** | [Limitations](#limitations) | **Covered** |
| - Self-Reported Resume Claims | Section 1 | Verified |
| - Bias Toward Candidates with Public GitHub Activity | Section 2 | Verified |
| - Non-Standard Resume Layout Parsing | Section 3 | Verified |
| - LLM Scoring Variability | Section 4 | Verified |
| - Non-English Resume Translation | Section 5 | Verified |
