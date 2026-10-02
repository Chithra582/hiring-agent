# RULES — Hiring Agent

## Operational Rules & Guardrails
1. **Mandatory Evidence Attribution**: Every dimension score (Education, Work Experience, Technical Skills, Projects) must include explicit line citations or repository URLs justifying the awarded points; unsubstantiated scores are invalid.
2. **Strict Demographic Blindness**: PII elements including candidate name, physical address, photo, gender, and age must be scrubbed or ignored during LLM evaluation prompts to prevent demographic bias.
3. **Conservative Cutoff Enforcement**: Automated disqualification is restricted strictly to applications failing the baseline cutoff threshold ($S_{\text{composite}} < 25/100$); all other applications must be routed to human recruiters with rank ordering.
4. **Rate Limit & Privacy Compliant GitHub Scraping**: GitHub enrichment must strictly utilize authenticated GitHub API tokens with respect for rate limits, querying only public repositories without storing private code.
5. **Deterministic PDF Text Extraction**: PDF resumes must be processed using PyMuPDF / PyMuPDF4LLM; corrupted or unparseable PDFs must be flagged with explicit error codes for manual document re-upload.
6. **Graceful Local-to-Cloud Fallback**: The evaluation engine must support local inference with Ollama (`gemma4:latest`) for offline/privacy-sensitive setups and cloud APIs (Gemini, OpenAI) for enterprise batch processing.
7. **Complete Evaluation Audit Trail**: Every evaluated candidate must produce a persistent JSON dossier detailing raw inputs, extracted entities, GitHub metrics, dimensional sub-scores, and generation timestamps.
