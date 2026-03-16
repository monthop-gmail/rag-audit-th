# Knowledge Base Specification for RAG

Version: 1.0

Purpose:
Define a standard for organizing documents inside a `knowledge-base/` folder
so they can be safely ingested into a Retrieval-Augmented Generation (RAG) system.

Scope:
- Internal knowledge repositories
- Domain-specific document repositories
- AI / RAG ingestion pipelines

---

# 1. Standard Folder Structure

All repositories intended for RAG ingestion must follow a structured layout.
Adapt folder names to your domain.

```
knowledge-base/
 ├ primary-sources/
 ├ secondary-sources/
 ├ reference/
 ├ policies/
 ├ internal/
 └ metadata/
```

### Domain Examples

| Domain | primary-sources/ | secondary-sources/ | reference/ |
|--------|-----------------|-------------------|------------|
| Legal | laws, regulations | court decisions | legal opinions |
| Education | textbooks, curricula | lecture notes | research papers |
| Enterprise | product docs | SOPs | knowledge articles |
| Healthcare | clinical guidelines | research papers | drug references |

## Folder Purpose

| Folder | Description | RAG Allowed |
|--------|-------------|-------------|
| primary-sources | authoritative, published content | YES |
| secondary-sources | supporting or interpretive content | YES |
| reference | expert opinions, commentary | CONDITIONAL |
| policies | organizational policy documents | CONDITIONAL |
| internal | internal strategy, drafts | NO |
| metadata | metadata index files | YES |

---

# 2. Document Metadata Standard

Every document must include metadata.

Required metadata fields:

```
title
source
publication_date
classification
document_type
version
language
domain
```

Example metadata (education domain):

```
title: Data Structures and Algorithms Chapter 5
source: MIT OpenCourseWare
publication_date: 2024-01-15
classification: public
document_type: textbook
version: 3.0
language: en
domain: computer-science
```

---

# 3. Data Classification Policy

Each document must include a classification tag.

Allowed values:

| Classification | Description |
|---------------|-------------|
| public | publicly available information |
| internal | internal organizational document |
| restricted | sensitive internal information |
| confidential | highly sensitive data |

RAG ingestion rules:

| Classification | Allowed |
|---------------|---------|
| public | YES |
| internal | CONDITIONAL |
| restricted | NO |
| confidential | NO |

---

# 4. Allowed Document Types

Define per domain. General pattern:

Allowed for ingestion:

```
published-content
official-reference
approved-guideline
public-policy
```

Restricted:

```
draft
internal-memo
confidential-strategy
personal-data-record
```

---

# 5. Privacy Compliance Requirements

The knowledge base must comply with applicable data protection law
(e.g., PDPA for Thailand, GDPR for EU).

Before ingestion, documents must be scanned for PII.

Examples of PII:

```
national ID
phone number
home address
passport number
health information
financial records
```

PII Detection Requirement:

```
PII_scan_before_embedding = true
```

Pass Criteria:

```
PII detection rate < 0.01%
```

---

# 6. Version Control Requirements

Documents must include version tracking.

Required fields:

```
content_status    # active | draft | archived | superseded
effective_date
superseded_date
version
```

---

# 7. Chunking Policy

Documents must be chunked according to their natural structure.

| Domain | Correct Chunking | Incorrect Chunking |
|--------|-----------------|-------------------|
| Legal | section, article, clause | random 1000-token split |
| Education | chapter, section, subsection | random token chunk |
| Enterprise | topic, procedure step | fixed character split |

Rule: preserve semantic boundaries when chunking.

---

# 8. Embedding Policy

All documents must use a consistent embedding model.

Required metadata:

```
embedding_model
embedding_version
embedding_date
```

---

# 9. Ingestion Pipeline Standard

Recommended ingestion pipeline:

```
knowledge-base
    ↓
document classifier
    ↓
metadata validator
    ↓
PII scanner
    ↓
domain-aware chunker
    ↓
embedding
    ↓
vector database
```

Audit Requirement:

Pipeline must include:

- metadata validation
- PII scanning
- classification filtering

---

# 10. Access Control

Sensitive documents must not be retrievable by all users.

Define access levels per your organization:

```
public
internal
team-specific
restricted
```

---

# 11. Logging Requirements

The RAG system must log:

```
query
retrieved_documents
model_output
timestamp
user_role
```

Logs must be stored securely.

Retention recommendation:

```
180 days
```

---

# 12. Audit Procedure

Auditors should perform the following checks.

1. Scan folder structure
2. Validate metadata completeness (target: > 90%)
3. Detect PII
4. Verify classification filtering
5. Validate chunking strategy
6. Validate embedding consistency

---

# 13. Audit Result Template

```
Audit Report

System Name:
Domain:
Date:

Controls Passed:
Controls Failed:

Critical Issues:
High Issues:
Medium Issues:
Low Issues:

Recommendation:
```
