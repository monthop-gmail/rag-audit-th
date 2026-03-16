# Thai Legal RAG Audit Report

**System:** PromptForge Legal Pilot
**Date:** 2026-03-16
**Auditor:** Claude Code (AI-agent audit)
**Checklist:** thai-legal-rag-audit-checklist.md v1.0
**Overall Result:** FAIL — Block production release

---

## Audit metadata

```yaml
audit_meta:
  system_name: "PromptForge Legal Pilot"
  system_version: "1.0 (commit 908726a)"
  audit_date: "2026-03-16"
  environment: "dev"
  deployment_model: "local (Docker Compose) + Cloudflare Tunnel"
  primary_language: "mixed (th/en)"
  use_cases:
    - "Legal knowledge search (PDPA, labor law)"
    - "Prompt enrichment for legal Q&A"
    - "Contract template Q&A"
  auditors:
    - "Claude Code (AI-agent audit)"
  business_owner: "not assigned"
  technical_owner: "not assigned"
  legal_owner: "not assigned"
  dpo_or_privacy_owner: "not assigned"
```

---

## Evidence pack

```yaml
evidence_pack:
  architecture_diagram: false
  system_prompt: true          # retriever.ts buildContext()
  retrieval_config: true       # config.ts (topK, chunkSize, overlap)
  chunking_config: true        # services/chunker.ts
  index_schema: true           # types.ts (ChunkMetadata)
  source_registry: false
  ingestion_pipeline_docs: false
  evaluation_report: false
  red_team_results: false
  logging_policy: false
  privacy_notice: false
  retention_policy: false
  access_control_matrix: false
  sample_queries_and_answers: false
  incident_response_runbook: false
  vendor_list: false
```

---

## Scorecard summary

```yaml
scorecard:
  PASS: 0
  PARTIAL: 12
  FAIL: 41
  N_A: 4
```

| Severity | PASS | PARTIAL | FAIL | N/A |
|----------|------|---------|------|-----|
| CRITICAL | 0    | 4       | 16   | 0   |
| HIGH     | 0    | 8       | 21   | 2   |
| MEDIUM   | 0    | 0       | 4    | 2   |

---

## A. Governance and scope

### TH-LRAG-GOV-001 — Defined legal use case

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **PARTIAL** |
| Evidence | README, 4 legal templates in `legalTemplates.ts` |
| Finding | Allowed use cases are implicitly defined via templates (NDA, PDPA QA, compliance check, contract summary). Prohibited use cases are not documented anywhere. |
| Remediation | Create a `USAGE_POLICY.md` defining allowed and prohibited use cases. |

### TH-LRAG-GOV-002 — Human accountability

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No RACI or ownership document in repo. |
| Finding | No business, technical, legal, or privacy owner assigned. |
| Remediation | Assign and document all four owner roles. |

### TH-LRAG-GOV-003 — Risk classification

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No risk assessment or classification document found. |
| Finding | System risk tier is undocumented. |
| Remediation | Classify the system (internal-only vs. customer-facing) and document the risk tier. |

### TH-LRAG-GOV-004 — Legal escalation path

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No escalation SOP, no UI flow for human escalation. |
| Finding | No escalation path exists for ambiguous or high-impact legal questions. |
| Remediation | Add escalation flow for ambiguous/high-impact questions. |

### TH-LRAG-GOV-005 — Change management

| Field | Value |
|-------|-------|
| Severity | MEDIUM |
| Status | **PARTIAL** |
| Evidence | Code is in Git. No approval process, no release notes, no branch protection. |
| Finding | Version control exists but no formal change approval process. |
| Remediation | Add PR review requirements and release notes. |

---

## B. Source legality and legal authority

### TH-LRAG-SRC-001 — Official legal source registry

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | 7 markdown files in `/knowledge-base/` organized by category. No formal source registry or allowlist. |
| Finding | No approved source registry. Files exist but without provenance tracking. |
| Remediation | Create a source registry listing each document with origin, authority, and approval status. |

### TH-LRAG-SRC-002 — Official source preference

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | `retriever.ts` returns top-K by cosine similarity only. No source-priority weighting. |
| Finding | No preference mechanism for official Thai government sources over informal content. |
| Remediation | Add source-priority boosting in retrieval ranking logic. |

### TH-LRAG-SRC-003 — Source provenance metadata

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | Chunk metadata: `{ source, title, heading, category, position }`. Missing: source URL, publication date, effective date, ingestion date. |
| Finding | Only filename and category are tracked. Critical provenance fields are absent. |
| Remediation | Extend metadata schema with `source_url`, `publication_date`, `effective_date`, `ingestion_date`. |

### TH-LRAG-SRC-004 — Law status metadata

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | No `status` field (active/amended/repealed/draft) in chunk metadata schema. |
| Finding | Law status is not tracked. System cannot distinguish repealed from active law. |
| Remediation | Add `law_status` field to metadata schema and populate it. |

### TH-LRAG-SRC-005 — Version chain tracking

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No version linkage between documents. No amendment tracking in metadata or ingestion logic. |
| Finding | Amended laws cannot be traced to prior versions. |
| Remediation | Add version chain metadata linking amended provisions to their predecessors. |

### TH-LRAG-SRC-006 — Source licensing review

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **N/A** |
| Evidence | Current knowledge base contains only internally-created markdown summaries and templates. |
| Finding | No third-party copyrighted content observed. |

### TH-LRAG-SRC-007 — Source category separation

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **PARTIAL** |
| Evidence | `category` field with values: `laws`, `contracts`, `policies`, `faq`. No distinction between primary law, subordinate regulation, court decisions, commentary, or AI-generated content. |
| Finding | Basic category separation exists but taxonomy is too coarse for legal requirements. |
| Remediation | Expand taxonomy to include legal source types (primary law, regulation, court decision, commentary, internal policy). |

### TH-LRAG-SRC-008 — Draft versus enacted separation

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | No draft/enacted labeling in metadata. No retrieval rules to prevent draft documents from being cited as binding law. |
| Finding | System cannot distinguish draft from enacted law. |
| Remediation | Add `document_status` field (draft/enacted/template) and filter logic. |

---

## C. Thai legal content quality

### TH-LRAG-LAW-001 — Citation granularity

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **PARTIAL** |
| Evidence | `chunker.ts` splits by markdown headings (##/###) corresponding to sections. `heading` field captures section-level data. Citation output only shows `title (source)`, not specific section. |
| Finding | Heading-level metadata exists but is not surfaced as granular citations in answers. |
| Remediation | Include `heading` (section/มาตรา) in citation output format. |

### TH-LRAG-LAW-002 — Effective date awareness

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | No effective date field in metadata. No logic to consider effective dates when answering. |
| Finding | System has no concept of effective dates. |
| Remediation | Add `effective_date` to metadata; incorporate date-aware retrieval. |

### TH-LRAG-LAW-003 — Repeal and amendment awareness

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | No repeal/amendment status in metadata. |
| Finding | System will serve repealed text as current law. Critical safety gap. |
| Remediation | Add law status tracking and filter repealed content from default results. |

### TH-LRAG-LAW-004 — Court decision labeling

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **N/A** |
| Evidence | Current knowledge base contains no court decisions. |

### TH-LRAG-LAW-005 — Translation risk flag

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No translation caveat in system prompts or enrichment output. |
| Finding | If queried in English about Thai law, no warning that Thai original controls is provided. |
| Remediation | Add translation caveat to enrichment context when source is Thai and query is English. |

### TH-LRAG-LAW-006 — Internal policy separation

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **PARTIAL** |
| Evidence | `category` field separates `laws` from `policies` and `contracts`. Enrichment output does not visually distinguish internal policy from law. |
| Finding | Metadata separation exists; answer-level separation does not. |
| Remediation | Label source category in citation output (e.g., `[กฎหมาย]` vs `[นโยบายภายใน]`). |

---

## D. Privacy, PDPA, and sensitive data

### TH-LRAG-PDPA-001 — Personal data inventory

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | No data inventory document. Knowledge base not reviewed for personal data. Logs (`console.log`) may contain query text. |
| Finding | No personal data inventory exists. |
| Remediation | Conduct data inventory of corpus, logs, and prompts. |

### TH-LRAG-PDPA-002 — Lawful basis documented

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | No privacy assessment, legal memo, or lawful basis documentation. |
| Finding | No PDPA lawful basis documented for data processing. |
| Remediation | Document lawful basis for each data flow (corpus, queries, logs, API calls to OpenAI). |

### TH-LRAG-PDPA-003 — Sensitive data handling

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | No DLP rules, no content filters, no sensitive data scanning. |
| Finding | No sensitive data controls exist. |
| Remediation | Implement content scanning and DLP rules for corpus and queries. |

### TH-LRAG-PDPA-004 — Minimization

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | Entire documents are chunked and stored. No personal data masking or removal in the ingestion pipeline. |
| Finding | No data minimization controls. |
| Remediation | Add PII detection/masking in ingestion pipeline. |

### TH-LRAG-PDPA-005 — Data subject rights handling

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No rights-handling process, no SOP, no privacy notice. |
| Finding | No data subject rights mechanism exists. |
| Remediation | Establish DSR handling process. |

### TH-LRAG-PDPA-006 — Retention policy

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | ChromaDB persists indefinitely (Docker volume, no TTL). Frontend IndexedDB has no retention limits. No retention schedule. |
| Finding | No retention policy exists. Data persists indefinitely. |
| Remediation | Define and enforce retention periods for queries, logs, and vector data. |

### TH-LRAG-PDPA-007 — Cross-border transfer assessment

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | Queries sent to OpenAI API (US-based) for embedding generation. No cross-border transfer assessment or DPIA. |
| Finding | Cross-border data transfer to OpenAI is unassessed. |
| Remediation | Conduct transfer impact assessment for OpenAI API usage. |

### TH-LRAG-PDPA-008 — Privacy notice transparency

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No privacy notice in UI or documentation. |
| Finding | Users are not informed about data collection or AI processing. |
| Remediation | Add privacy notice to the application. |

---

## E. Security and access control

### TH-LRAG-SEC-001 — Authentication

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | RAG API has no authentication (`app.use(cors())` wide open). Frontend has no login. MCP server has no auth. Cloudflare Tunnel provides network-level access only. |
| Finding | No authentication on any endpoint. |
| Remediation | Implement authentication (at minimum API key auth on RAG API). |

### TH-LRAG-SEC-002 — Authorization by data class

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | Retrieval returns all matching documents regardless of user role. No ACL on documents. `filter` parameter is optional and user-controlled. |
| Finding | No document-level authorization. Any user can retrieve any document. |
| Remediation | Implement document-level ACLs and role-based retrieval filtering. |

### TH-LRAG-SEC-003 — Encryption in transit and at rest

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **PARTIAL** |
| Evidence | Cloudflare Tunnel provides HTTPS externally. Internal Docker network is unencrypted HTTP. ChromaDB storage is unencrypted at rest. |
| Finding | Transit encryption external only. No encryption at rest. |
| Remediation | Enable TLS for internal service communication; encrypt storage volumes. |

### TH-LRAG-SEC-004 — Secret management

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **PARTIAL** |
| Evidence | SOPS with age encryption configured (`.sops.yaml`). `.env` is gitignored. OpenAI API key passed as plain environment variable to containers. |
| Finding | Secret management tooling exists but secrets exposed as plain env vars at runtime. |
| Remediation | Use a secrets manager (Vault, Docker secrets) for runtime secret injection. |

### TH-LRAG-SEC-005 — Network boundary

| Field | Value |
|-------|-------|
| Severity | MEDIUM |
| Status | **PARTIAL** |
| Evidence | Docker Compose network `promptforge` provides isolation. No explicit network policies or firewall rules documented. |
| Finding | Docker network provides basic isolation; no formal boundary documentation. |
| Remediation | Document network architecture and add explicit network policies. |

### TH-LRAG-SEC-006 — Audit logging for admin actions

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | Only `console.log` for ingestion operations. No audit trail for prompt/index/config changes. |
| Finding | No admin audit logging. |
| Remediation | Implement structured audit logging for admin operations. |

---

## F. Retrieval and indexing controls

### TH-LRAG-RET-001 — Document chunking policy

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **PARTIAL** |
| Evidence | `chunker.ts`: heading-aware splitting (##/###), 1000-char max, 200-char overlap. Aligns with Thai legal text structure (มาตรา as headings). No policy document. |
| Finding | Chunking implementation is reasonable but undocumented and not validated against Thai legal text quality standards. |
| Remediation | Document chunking policy; validate with legal SME review of chunk samples. |

### TH-LRAG-RET-002 — Metadata filtering

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **PARTIAL** |
| Evidence | `/api/search` accepts optional `filter` parameter for metadata. `category` filter works. Missing: date, version, jurisdiction, department, confidentiality, status filters. |
| Finding | Basic category filtering exists; critical legal metadata filters are missing. |
| Remediation | Add metadata filters for date range, law status, jurisdiction, and confidentiality. |

### TH-LRAG-RET-003 — Duplicate and near-duplicate handling

| Field | Value |
|-------|-------|
| Severity | MEDIUM |
| Status | **FAIL** |
| Evidence | No deduplication logic. Re-running ingest creates duplicate chunks (UUID-based IDs, no content hashing). |
| Finding | No duplicate handling. Re-ingestion creates duplicates. |
| Remediation | Add content hashing for chunk IDs or dedup logic in ingestion. |

### TH-LRAG-RET-004 — Freshness/update SLA

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | Ingestion is manual (`POST /api/ingest`). No scheduled updates, no SLA, no freshness monitoring. |
| Finding | No update SLA or automated ingestion pipeline. |
| Remediation | Define update SLA and implement scheduled ingestion with monitoring. |

### TH-LRAG-RET-005 — Source deletion and takedown

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No delete endpoint on RAG API. No documented takedown workflow. Only full re-ingestion possible. |
| Finding | No document deletion capability. |
| Remediation | Add document/chunk deletion API and takedown workflow. |

### TH-LRAG-RET-006 — Thai language retrieval quality

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No evaluation report, no test set, no retrieval metrics. Relies on `text-embedding-3-small` without Thai-specific evaluation. |
| Finding | Thai retrieval quality is unevaluated. |
| Remediation | Create Thai legal retrieval test set and measure Recall@K. |

### TH-LRAG-RET-007 — Query rewrite safety

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **N/A** |
| Evidence | No query rewriting or expansion logic in codebase. Queries are embedded directly. |

---

## G. Generation safety and answer quality

### TH-LRAG-GEN-001 — Answer grounded in retrieved context

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **PARTIAL** |
| Evidence | Enrichment prompt instructs: "ใช้ข้อมูลอ้างอิงข้างต้นเป็นหลัก". Actual answer generation delegated to external LLM. No groundedness enforcement. |
| Finding | Prompt instructs grounding but system cannot enforce it. No groundedness checking. |
| Remediation | Add groundedness validation or post-generation checking. |

### TH-LRAG-GEN-002 — Citation in every substantive legal answer

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **PARTIAL** |
| Evidence | Enrichment prompt includes "อ้างอิงแหล่งที่มาในรูปแบบ [หมายเลข]". Sources appended with numbered references. Citation is instructed, not enforced. |
| Finding | Citation is instructed but not guaranteed or validated. |
| Remediation | Validate that generated answers contain citations; add citation checking. |

### TH-LRAG-GEN-003 — Unsupported answer refusal

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | No refusal logic. No confidence threshold. No instruction to refuse when evidence is insufficient. |
| Finding | System does not refuse unsupported queries. |
| Remediation | Add refusal instructions and low-confidence handling to system prompt. |

### TH-LRAG-GEN-004 — Distinguish law from opinion

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | Enrichment output does not distinguish quoted law from summary/interpretation. All retrieved chunks presented uniformly. |
| Finding | No separation between law, summary, interpretation, and recommendation. |
| Remediation | Structure answer format to separate quoted law from AI interpretation. |

### TH-LRAG-GEN-005 — Advice limitation banner

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No disclaimer in UI, enrichment output, or system prompts. |
| Finding | No "this is not legal advice" banner exists. |
| Remediation | Add disclaimer to system prompt and UI. |

### TH-LRAG-GEN-006 — High-risk prompt routing

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No query classifier, no routing logic, no high-risk detection. All queries treated identically. |
| Finding | No high-risk prompt detection or routing. |
| Remediation | Implement query classifier for litigation, criminal, deadline-sensitive, and penalty-related queries. |

### TH-LRAG-GEN-007 — Output schema for legal answers

| Field | Value |
|-------|-------|
| Severity | MEDIUM |
| Status | **PARTIAL** |
| Evidence | Templates in `legalTemplates.ts` define structured output (role, context, task, constraints, output format). Enriched prompt does not enforce summary/sources/limitations/confidence schema. |
| Finding | Template structure exists but legal answer schema is incomplete. |
| Remediation | Add structured output schema with summary, sources, limitations, confidence. |

### TH-LRAG-GEN-008 — Confidence signaling

| Field | Value |
|-------|-------|
| Severity | MEDIUM |
| Status | **FAIL** |
| Evidence | Similarity scores returned in API response but not surfaced to users. No uncertainty signaling. |
| Finding | Confidence data exists internally but is not communicated. |
| Remediation | Surface similarity scores as confidence indicators in UI and prompt output. |

---

## H. Prompt injection and abuse resistance

### TH-LRAG-RED-001 — Prompt injection resistance

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | No input sanitization, no prompt injection defenses, no red-team test results. User queries passed directly to embedding and prompt construction. |
| Finding | No prompt injection resistance. |
| Remediation | Add input sanitization, prompt injection detection, and red-team testing. |

### TH-LRAG-RED-002 — Data exfiltration resistance

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | No access controls on retrieval. No output filtering. All documents retrievable by any user. |
| Finding | No exfiltration resistance. All content accessible without restriction. |
| Remediation | Implement document-level access controls and output filtering. |

### TH-LRAG-RED-003 — System prompt leakage resistance

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | Enrichment prompt constructed in `retriever.ts` with no protection. No anti-extraction instructions. |
| Finding | No prompt leakage resistance. |
| Remediation | Add anti-extraction instructions; separate system prompts from user context. |

### TH-LRAG-RED-004 — Unsafe document instruction handling

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | Retrieved documents injected directly into prompt context without sanitization. |
| Finding | No protection against malicious document instructions. |
| Remediation | Sanitize retrieved content before prompt injection; add delimiter enforcement. |

---

## I. Model and vendor risk

### TH-LRAG-MOD-001 — Model inventory

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **PARTIAL** |
| Evidence | `config.ts` specifies `text-embedding-3-small`. Generation model is external and undocumented. No formal model registry. |
| Finding | Embedding model known; generation model undocumented. |
| Remediation | Create formal model inventory including all models in the pipeline. |

### TH-LRAG-MOD-002 — Third-party processor review

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | OpenAI processes all embedding requests. No vendor review, DPA, or security assessment documented. |
| Finding | OpenAI used without formal vendor review. |
| Remediation | Conduct vendor due diligence for OpenAI; execute DPA. |

### TH-LRAG-MOD-003 — Training and retention by provider

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No documentation of OpenAI's data retention/training policies for this deployment. |
| Finding | Provider data handling terms are undocumented. |
| Remediation | Review and document OpenAI API data usage terms; configure opt-out if available. |

### TH-LRAG-MOD-004 — Fallback model safety

| Field | Value |
|-------|-------|
| Severity | MEDIUM |
| Status | **N/A** |
| Evidence | No fallback model configured. System fails if OpenAI is unavailable. |

---

## J. Evaluation and monitoring

### TH-LRAG-EVAL-001 — Gold test set for Thai legal QA

| Field | Value |
|-------|-------|
| Severity | CRITICAL |
| Status | **FAIL** |
| Evidence | No test files, no benchmark, no evaluation dataset in codebase. |
| Finding | No Thai legal QA benchmark exists. |
| Remediation | Create evaluation set with Thai legal questions and expected citations. |

### TH-LRAG-EVAL-002 — Retrieval metrics

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No retrieval metrics measured. No evaluation reports. |
| Finding | Retrieval quality is unmeasured. |
| Remediation | Implement Recall@K and citation-hit-rate measurement. |

### TH-LRAG-EVAL-003 — Answer quality metrics

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No answer quality evaluation methodology. |
| Finding | Answer quality is unmeasured. |
| Remediation | Implement groundedness, citation correctness, and refusal quality metrics. |

### TH-LRAG-EVAL-004 — Pre-release gate

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No CI/CD pipeline, no release gate, no quality thresholds. |
| Finding | No pre-release quality gate. |
| Remediation | Add CI/CD with minimum quality thresholds before deployment. |

### TH-LRAG-EVAL-005 — Production monitoring

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | Only `/health` endpoint returning `{status: 'ok'}`. No dashboards, alerts, or metrics collection. |
| Finding | No production monitoring beyond basic health check. |
| Remediation | Implement observability (metrics, alerts, dashboards). |

### TH-LRAG-EVAL-006 — Periodic re-audit

| Field | Value |
|-------|-------|
| Severity | MEDIUM |
| Status | **FAIL** |
| Evidence | No audit schedule or governance calendar. |
| Finding | No periodic re-audit cadence exists. |
| Remediation | Establish quarterly re-audit schedule. |

---

## K. Logging, traceability, and incident response

### TH-LRAG-LOG-001 — End-to-end trace logging

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | `console.log`/`console.error` only. No request IDs, no trace correlation, no structured logging. |
| Finding | No end-to-end tracing capability. |
| Remediation | Implement structured logging with request/trace IDs. |

### TH-LRAG-LOG-002 — Log minimization

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **PARTIAL** |
| Evidence | Logs are minimal (mostly ingestion stats). Query text is not explicitly logged. No log policy or masking exists. |
| Finding | Logs are accidentally minimal. No deliberate minimization policy. |
| Remediation | Document log policy; add PII masking before expanding logging. |

### TH-LRAG-LOG-003 — Incident response runbook

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **FAIL** |
| Evidence | No incident response runbook found. |
| Finding | No IR runbook for hallucination, privacy, wrong-law, or data leakage incidents. |
| Remediation | Create scenario-based incident response runbook. |

### TH-LRAG-LOG-004 — User complaint handling

| Field | Value |
|-------|-------|
| Severity | MEDIUM |
| Status | **FAIL** |
| Evidence | No complaint mechanism in UI or documentation. |
| Finding | Users cannot report incorrect answers. |
| Remediation | Add feedback/complaint mechanism to the UI. |

---

## L. Deployment and operational controls

### TH-LRAG-OPS-001 — Environment separation

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **PARTIAL** |
| Evidence | Docker Compose has dev and prod override (`docker-compose.prd.yml`). Same `.env`, same ChromaDB volume, no credential separation. |
| Finding | Basic separation exists; credentials and data are not separated. |
| Remediation | Separate credentials and data stores per environment. |

### TH-LRAG-OPS-002 — Backup and recovery

| Field | Value |
|-------|-------|
| Severity | MEDIUM |
| Status | **FAIL** |
| Evidence | ChromaDB uses Docker volume. No backup scripts, no DR tests, no restore procedure. |
| Finding | No backup or recovery capability. |
| Remediation | Implement backup for ChromaDB volume and knowledge base; test restore. |

### TH-LRAG-OPS-003 — Rollback capability

| Field | Value |
|-------|-------|
| Severity | MEDIUM |
| Status | **PARTIAL** |
| Evidence | Git provides code rollback. ChromaDB can be rebuilt via re-ingestion. No versioned prompt/index rollback. |
| Finding | Code rollback exists; index/prompt rollback is manual. |
| Remediation | Add versioning for prompts and index snapshots. |

### TH-LRAG-OPS-004 — Source connector security

| Field | Value |
|-------|-------|
| Severity | HIGH |
| Status | **PARTIAL** |
| Evidence | Knowledge base mounted read-only in Docker. No external connectors. Ingestion endpoint is unauthenticated. |
| Finding | Read-only mount is good. Unauthenticated ingestion endpoint is a risk. |
| Remediation | Add authentication to the ingestion endpoint. |

---

## M. Minimum production gate assessment

| Gate Criterion | Status |
|----------------|--------|
| Official-source registry exists | FAIL |
| Provenance metadata exists | FAIL |
| Law status metadata exists | FAIL |
| Effective date handling exists | FAIL |
| Citation in substantive answers exists | PARTIAL |
| Unsupported-answer refusal exists | FAIL |
| Access control on retrieval exists | FAIL |
| PDPA lawful basis exists | FAIL |
| Retention policy exists | FAIL |
| Prompt injection testing exists | FAIL |
| Thai legal benchmark exists | FAIL |
| Incident response runbook exists | FAIL |

**Production gate result: 0/12 passed. System is not ready for production.**

---

## Final report

```yaml
final_report:
  overall_result: FAIL
  summary:
    critical_fail_count: 16
    high_fail_count: 25
    medium_fail_count: 5
    partial_count: 12
    pass_count: 0
    n_a_count: 4
  top_findings:
    - control_id: "TH-LRAG-SRC-001 through SRC-004, SRC-008"
      severity: CRITICAL
      issue: "No source registry, no provenance metadata, no law status tracking, no draft/enacted separation"
      impact: "System cannot distinguish current from repealed law, or draft from enacted law"
      remediation: "Create source registry with full provenance and law status metadata"
    - control_id: "TH-LRAG-SEC-001, SEC-002"
      severity: CRITICAL
      issue: "No authentication or authorization on any endpoint"
      impact: "Any network user can access all legal documents and trigger ingestion"
      remediation: "Implement authentication and document-level access controls"
    - control_id: "TH-LRAG-PDPA-001 through PDPA-006"
      severity: CRITICAL
      issue: "No PDPA compliance controls exist"
      impact: "Organization is exposed to PDPA liability, especially with cross-border transfer to OpenAI"
      remediation: "Conduct data inventory, document lawful basis, implement retention policy"
    - control_id: "TH-LRAG-RED-001, RED-002"
      severity: CRITICAL
      issue: "No prompt injection or data exfiltration resistance"
      impact: "System is vulnerable to adversarial attacks"
      remediation: "Add input sanitization, red-team testing, access controls"
    - control_id: "TH-LRAG-EVAL-001"
      severity: CRITICAL
      issue: "No Thai legal QA benchmark exists"
      impact: "Answer quality is unknown and unmeasured"
      remediation: "Create evaluation set with Thai legal questions and expected answers"
  release_recommendation: "block"
  mandatory_remediation_before_prod:
    - "Create official source registry with provenance metadata"
    - "Add law status (active/repealed/amended) to all legal documents"
    - "Add effective date tracking"
    - "Implement authentication and authorization"
    - "Document PDPA lawful basis and implement retention policy"
    - "Add prompt injection resistance and red-team testing"
    - "Create Thai legal QA benchmark"
    - "Add unsupported-answer refusal logic"
    - "Add citation enforcement and validation"
    - "Create incident response runbook"
    - "Conduct cross-border transfer assessment for OpenAI API"
  follow_up_actions:
    - "Assign business, technical, legal, and privacy owners"
    - "Implement structured logging with trace IDs"
    - "Add production monitoring and alerting"
    - "Create escalation path for high-risk legal queries"
    - "Add advice limitation disclaimer to UI and prompts"
```

---

## Critical failed controls

```yaml
critical_failed_controls:
  - TH-LRAG-SRC-001  # No source registry
  - TH-LRAG-SRC-002  # No official source preference
  - TH-LRAG-SRC-003  # No provenance metadata
  - TH-LRAG-SRC-004  # No law status metadata
  - TH-LRAG-SRC-008  # No draft/enacted separation
  - TH-LRAG-LAW-002  # No effective date awareness
  - TH-LRAG-LAW-003  # No repeal/amendment awareness
  - TH-LRAG-PDPA-001 # No personal data inventory
  - TH-LRAG-PDPA-002 # No lawful basis
  - TH-LRAG-PDPA-003 # No sensitive data handling
  - TH-LRAG-PDPA-006 # No retention policy
  - TH-LRAG-SEC-001  # No authentication
  - TH-LRAG-SEC-002  # No authorization
  - TH-LRAG-RET-004  # No update SLA
  - TH-LRAG-GEN-003  # No unsupported answer refusal
  - TH-LRAG-GEN-004  # No law/opinion separation
  - TH-LRAG-RED-001  # No prompt injection resistance
  - TH-LRAG-RED-002  # No data exfiltration resistance
  - TH-LRAG-EVAL-001 # No Thai legal benchmark
```

---

## Verdict

**FAIL — Block production release.**

The system is a functional prototype with zero PASS controls and 16+ CRITICAL failures. The minimum production gate (Section M) is satisfied on 0 of 12 criteria. This system is appropriate for internal developer testing only. It must not be exposed to end users or used for legal decision-making in its current state.
