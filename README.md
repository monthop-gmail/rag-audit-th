# rag-th

Compliance framework, audit checklist, and evaluation toolkit for
Retrieval-Augmented Generation (RAG) systems — with Thai regulatory context (PDPA).

## What is this?

Building a RAG system is easy. Making sure it doesn't leak data, hallucinate,
or serve stale information is harder. This repo provides:

1. **Generic RAG compliance framework** — standards for knowledge-base structure,
   ingestion pipeline, governance, security, and evaluation that apply to any domain.
2. **Domain-specific audit checklists** — ready-to-use checklists with control IDs,
   severity levels, and pass/fail criteria. Starting with Thai Legal RAG (60+ controls).
3. **Audit agent** (ref) — a Python CLI tool that runs runtime tests against a live
   RAG endpoint: hallucination, prompt injection, citation accuracy, regression, and more.

## Who is this for?

- Teams building RAG systems who need a compliance baseline
- Auditors evaluating RAG deployments
- Organizations operating in Thailand (PDPA compliance)
- Anyone who wants structured quality gates for RAG

## Repository Structure

```
rag-th/
├── README.md
├── docs/
│   ├── framework/                  # Generic RAG compliance specs
│   │   ├── 01-knowledge-base.md    #   knowledge-base structure & metadata
│   │   ├── 02-ingestion-pipeline.md#   ingestion pipeline standard
│   │   ├── 03-governance.md        #   roles, approval, change management
│   │   ├── 04-security.md          #   threat model & mitigations
│   │   └── 05-evaluation.md        #   benchmark & evaluation metrics
│   └── domains/
│       └── legal/
│           └── thai-legal-rag-audit-checklist.md  # 60+ controls, TH-LRAG-*
└── ref/                            # Original reference material
    └── chatgpt/
        ├── 01/                     #   source specs & checklist
        └── 02/                     #   audit agent code (v1–v7)
```

## Framework Overview

The generic framework covers 5 areas:

| Area | Doc | Key Topics |
|------|-----|------------|
| Knowledge Base | [01-knowledge-base.md](docs/framework/01-knowledge-base.md) | folder structure, metadata schema, classification, PII scanning |
| Ingestion Pipeline | [02-ingestion-pipeline.md](docs/framework/02-ingestion-pipeline.md) | parser, classifier, validator, chunker, embedding, vector DB |
| Governance | [03-governance.md](docs/framework/03-governance.md) | roles, dataset approval, change management, model governance |
| Security | [04-security.md](docs/framework/04-security.md) | prompt injection, retrieval poisoning, data exfiltration, access control |
| Evaluation | [05-evaluation.md](docs/framework/05-evaluation.md) | Recall@k, citation accuracy, hallucination rate, consistency |

## Domain Checklists

### Thai Legal RAG

The [Thai Legal RAG Audit Checklist](docs/domains/legal/thai-legal-rag-audit-checklist.md)
provides 60+ controls across 13 categories:

| Category | Controls | Examples |
|----------|----------|---------|
| A. Governance | TH-LRAG-GOV-* | use case definition, human accountability, risk classification |
| B. Source legality | TH-LRAG-SRC-* | official source registry, provenance metadata, law status |
| C. Legal content quality | TH-LRAG-LAW-* | citation granularity, effective date, repeal awareness |
| D. Privacy / PDPA | TH-LRAG-PDPA-* | personal data inventory, lawful basis, retention |
| E. Security | TH-LRAG-SEC-* | authentication, authorization, encryption |
| F. Retrieval | TH-LRAG-RET-* | chunking, metadata filtering, freshness SLA |
| G. Generation safety | TH-LRAG-GEN-* | grounding, citation, refusal, advice limitation |
| H. Prompt injection | TH-LRAG-RED-* | injection resistance, exfiltration resistance |
| I. Model / vendor risk | TH-LRAG-MOD-* | model inventory, third-party review |
| J. Evaluation | TH-LRAG-EVAL-* | gold test set, retrieval metrics, pre-release gate |
| K. Logging | TH-LRAG-LOG-* | trace logging, incident response runbook |
| L. Operations | TH-LRAG-OPS-* | environment separation, backup, rollback |
| M. Production gate | — | 12 mandatory checks before go-live |

Severity levels: CRITICAL / HIGH / MEDIUM / LOW

### Adding Your Own Domain

Create a new checklist under `docs/domains/<your-domain>/` following the same pattern:
control IDs, severity, requirement, check method, evidence, and pass criteria.

## Key Evaluation Targets

| Metric | Target |
|--------|--------|
| Recall@5 | >= 0.9 |
| Citation accuracy | >= 95% |
| Grounded answer rate | >= 95% |
| Hallucination rate | < 3% |
| Consistency rate | >= 90% |
| Retrieval latency p95 | < 2s |
| Metadata completeness | > 90% |
| PII detection rate | < 0.01% |

## Getting Started

1. Read the [framework docs](docs/framework/) to understand the compliance baseline
2. Pick or create a [domain checklist](docs/domains/)
3. Collect evidence against each control
4. Score each control: `PASS` / `PARTIAL` / `FAIL` / `N/A`
5. Generate audit report using the templates provided

## License

TBD
