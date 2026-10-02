---
name: "role-fit-evaluation"
description: "Scores candidate qualifications against formal job role criteria using rubric-based multi-criteria weighting."
license: MIT
---

# Role-Fit Evaluation

## Overview
This skill matches extracted candidate qualifications and GitHub engineering signals against explicit job role specifications, evaluating educational prerequisites, skill coverage, and practical experience.

## Key Capabilities
- **Role Specification Ingestion**: Loads standardized role criteria (e.g., Software Engineering Intern, Senior Full-Stack Engineer).
- **Skill Overlap Analysis**: Matches candidate technologies against required and preferred job qualifications.
- **Experience Level Calibration**: Calibrates candidate project scope, internship experience, and coursework against expectations.
- **Rubric-Driven Assessment**: Evaluates candidate fit across distinct dimensional axes with granular point allocations.

## Operational Workflow
1. **Role Loading**: Retrieve target role rubric and qualification weights via `role_criteria_matcher`.
2. **Alignment Assessment**: Compare candidate experience and technical skills against required role criteria.
3. **Sub-Score Allocation**: Assign dimensional points (Education, Skills, Experience, Projects) according to rubric guidelines.
4. **Gap Identification**: Highlight specific skill proficiencies and note missing prerequisite qualifications.
