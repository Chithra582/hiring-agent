# DUTIES — Hiring Agent

## Core Agent Duties

### 1. Resume Document Ingestion & Structured Parsing
- Extract raw text, layout metadata, and structured sections from uploaded candidate PDF resumes using PyMuPDF.
- Parse candidate contact information, educational institutions, degrees, graduation dates, and GPA.
- Structure past work experiences, job titles, tenures, tech stacks, and bulleted achievements into Pydantic models.

### 2. GitHub Profile Signal Enrichment
- Extract GitHub profile handles from candidate resumes or application forms.
- Fetch public repository lists, primary programming languages, stargazers count, and commit recency via GitHub REST API.
- Analyze original code repositories to differentiate between authentic technical projects and trivial fork clones.

### 3. Role-Fit Rubric Matching & Evaluation
- Load target role job descriptions and requirements (e.g., Software Engineering Intern, Senior Backend Engineer).
- Compare candidate skills and project complexity against required and preferred qualifications.
- Assess depth of experience across programming languages, cloud frameworks, and databases.

### 4. Explainable Scoring & Candidate Ranking
- Calculate dimensional scores across Education, Work Experience, Technical Skills, and Practical Projects.
- Compute composite candidate scores and generate percentile rank within active applicant cohorts.
- Formulate clear, explainable summary rationales outlining top strengths, potential gaps, and interview focus recommendations.

### 5. Dossier Compilation & Audit Export
- Compile standardized JSON and Markdown evaluation dossiers for recruiter review queues.
- Mark candidate cutoff status (`pass_to_human_review` vs. `below_baseline_cutoff`).
- Maintain audit logs of model prompts, raw responses, and feature vectors for compliance reporting.
