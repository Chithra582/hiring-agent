---
name: "explainable-hiring-scoring"
description: "Synthesizes objective composite scores with granular evidence citations, strengths, weaknesses, and cutoff justifications."
license: MIT
---

# Explainable Hiring Scoring

## Overview
This skill synthesizes dimensional evaluations into an objective composite score and candidate rank, generating transparent evaluation dossiers with line-level evidence citations for recruiter review.

## Key Capabilities
- **Composite Score Calculation**: Computes mathematically weighted overall scores (0-100 scale) from dimensional sub-scores.
- **Evidence-Based Citation**: Backs up every positive and negative evaluation point with citations from resume text or GitHub repos.
- **Cutoff Determination**: Determines whether candidates pass through to human recruiter review queues ($S \ge \text{Cutoff}$).
- **Recruiter Dossier Authoring**: Produces clean Markdown and JSON summaries detailing candidate strengths, weaknesses, and suggested interview questions.

## Operational Workflow
1. **Score Aggregation**: Aggregate dimensional points using `candidate_scorer` into composite score $S_{\text{composite}}$.
2. **Cutoff Decision**: Evaluate $S_{\text{composite}}$ against baseline filtering cutoff (default: 25/100).
3. **Dossier Generation**: Invoke `eval_dossier_generator` to compile the explainable evaluation dossier with cited evidence.
4. **Queue Prioritization**: Assign candidate percentile ranking within the current applicant cohort.
