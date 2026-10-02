---
name: "github-signal-enrichment"
description: "Fetches public GitHub profile activity, repository contributions, star counts, and language competencies."
license: MIT
---

# GitHub Signal Enrichment

## Overview
This skill queries the public GitHub REST API to augment candidate resumes with verifiable software engineering signals, evaluating practical code contributions and project authenticity.

## Key Capabilities
- **Repository Profiling**: Analyzes candidate public repositories, distinguishing original software projects from forks.
- **Language Competency Breakdown**: Quantifies repository byte counts to verify candidate proficiency across programming languages.
- **Activity & Velocity Tracking**: Evaluates commit frequency, contribution calendars, and project maintenance activity.
- **Project Impact Assessment**: Gauges community reception via stargazer counts, forks, and pull request activity.

## Operational Workflow
1. **Handle Discovery**: Extract GitHub URL or username from parsed candidate resume.
2. **Profile Ingestion**: Query GitHub API endpoints via `github_analyzer` for public repositories and user metadata.
3. **Metric Calculation**: Compute original repo count, dominant language distributions, and total star counts.
4. **Enrichment Merging**: Bind verified technical signals into the candidate evaluation profile.
