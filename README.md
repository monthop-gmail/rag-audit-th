# rag-th

Compliance framework, audit checklist และ evaluation toolkit สำหรับระบบ
Retrieval-Augmented Generation (RAG) — รองรับบริบทกฎหมายและกฎระเบียบไทย (PDPA)

## นี่คืออะไร?

การสร้างระบบ RAG ไม่ยาก แต่การทำให้มันไม่รั่วข้อมูล ไม่ hallucinate
และไม่ตอบจากข้อมูลเก่านั้นยากกว่า repo นี้ให้:

1. **RAG compliance framework แบบ generic** — มาตรฐานด้าน knowledge-base structure,
   ingestion pipeline, governance, security และ evaluation ที่ใช้ได้กับทุก domain
2. **Audit checklist เฉพาะ domain** — checklist พร้อมใช้ มี control ID, severity level
   และเกณฑ์ pass/fail เริ่มต้นด้วย Thai Legal RAG (60+ controls)
3. **ใช้ร่วมกับ Claude Code** — เอา repo นี้เข้าไปใน project แล้วให้ Claude Code
   audit ระบบ RAG ตาม framework ได้อัตโนมัติ

## เหมาะกับใคร?

- ทีมที่สร้างระบบ RAG และต้องการ compliance baseline
- Auditor ที่ตรวจสอบระบบ RAG
- องค์กรที่ดำเนินงานในไทย (PDPA compliance)
- ใครก็ตามที่ต้องการ quality gate สำหรับระบบ RAG

## โครงสร้าง Repository

```
rag-th/
├── README.md
├── docs/
│   ├── framework/                  # RAG compliance specs (generic)
│   │   ├── 01-knowledge-base.md    #   โครงสร้าง knowledge-base & metadata
│   │   ├── 02-ingestion-pipeline.md#   มาตรฐาน ingestion pipeline
│   │   ├── 03-governance.md        #   roles, approval, change management
│   │   ├── 04-security.md          #   threat model & mitigations
│   │   └── 05-evaluation.md        #   benchmark & evaluation metrics
│   └── domains/
│       └── legal/
│           └── thai-legal-rag-audit-checklist.md  # 60+ controls, TH-LRAG-*
```

## ภาพรวม Framework

Framework ครอบคลุม 5 ด้าน:

| ด้าน | เอกสาร | หัวข้อหลัก |
|------|--------|-----------|
| Knowledge Base | [01-knowledge-base.md](docs/framework/01-knowledge-base.md) | โครงสร้าง folder, metadata schema, classification, PII scanning |
| Ingestion Pipeline | [02-ingestion-pipeline.md](docs/framework/02-ingestion-pipeline.md) | parser, classifier, validator, chunker, embedding, vector DB |
| Governance | [03-governance.md](docs/framework/03-governance.md) | roles, dataset approval, change management, model governance |
| Security | [04-security.md](docs/framework/04-security.md) | prompt injection, retrieval poisoning, data exfiltration, access control |
| Evaluation | [05-evaluation.md](docs/framework/05-evaluation.md) | Recall@k, citation accuracy, hallucination rate, consistency |

## Domain Checklists

### Thai Legal RAG

[Thai Legal RAG Audit Checklist](docs/domains/legal/thai-legal-rag-audit-checklist.md)
มี 60+ controls แบ่งเป็น 13 หมวด:

| หมวด | Controls | ตัวอย่าง |
|------|----------|---------|
| A. Governance | TH-LRAG-GOV-* | กำหนด use case, ผู้รับผิดชอบ, risk classification |
| B. แหล่งที่มาของกฎหมาย | TH-LRAG-SRC-* | source registry, provenance metadata, สถานะกฎหมาย |
| C. คุณภาพเนื้อหากฎหมาย | TH-LRAG-LAW-* | citation ระดับมาตรา, วันบังคับใช้, การยกเลิก/แก้ไข |
| D. Privacy / PDPA | TH-LRAG-PDPA-* | บัญชีข้อมูลส่วนบุคคล, ฐานทางกฎหมาย, retention |
| E. Security | TH-LRAG-SEC-* | authentication, authorization, encryption |
| F. Retrieval | TH-LRAG-RET-* | chunking, metadata filtering, freshness SLA |
| G. ความปลอดภัยของคำตอบ | TH-LRAG-GEN-* | grounding, citation, refusal, ข้อจำกัดคำแนะนำ |
| H. Prompt injection | TH-LRAG-RED-* | ทนทานต่อ injection, ป้องกัน exfiltration |
| I. Model / vendor risk | TH-LRAG-MOD-* | model inventory, third-party review |
| J. Evaluation | TH-LRAG-EVAL-* | gold test set, retrieval metrics, pre-release gate |
| K. Logging | TH-LRAG-LOG-* | trace logging, incident response runbook |
| L. Operations | TH-LRAG-OPS-* | แยก environment, backup, rollback |
| M. Production gate | — | 12 ข้อบังคับก่อน go-live |

ระดับความรุนแรง: CRITICAL / HIGH / MEDIUM / LOW

### เพิ่ม Domain ใหม่

สร้าง checklist ใหม่ใน `docs/domains/<your-domain>/` ตามรูปแบบเดียวกัน:
control ID, severity, requirement, วิธีตรวจ, หลักฐาน, เกณฑ์ผ่าน

## เป้าหมาย Evaluation

| Metric | เป้าหมาย |
|--------|----------|
| Recall@5 | >= 0.9 |
| Citation accuracy | >= 95% |
| Grounded answer rate | >= 95% |
| Hallucination rate | < 3% |
| Consistency rate | >= 90% |
| Retrieval latency p95 | < 2s |
| Metadata completeness | > 90% |
| PII detection rate | < 0.01% |

## ใช้งานร่วมกับ Claude Code

เอา repo นี้เข้าไปใน project ที่ต้องการ audit แล้วให้ Claude Code
อ่าน framework/checklist และตรวจสอบ codebase ให้อัตโนมัติ

### ติดตั้ง

**วิธี A — Git submodule** (แนะนำสำหรับทีม)

```bash
cd your-rag-project/
git submodule add https://github.com/monthop-gmail/rag-th.git audit
```

**วิธี B — Clone เข้าไปใน project**

```bash
cd your-rag-project/
git clone https://github.com/monthop-gmail/rag-th.git audit
```

**วิธี C — อ้างอิง URL โดยตรง**

ไม่ต้อง clone สั่งให้ Claude Code fetch ได้เลย

### เพิ่มใน CLAUDE.md

เพิ่มส่วนนี้ใน `CLAUDE.md` ของ project เพื่อให้ Claude Code รู้จัก audit framework:

```markdown
## RAG Audit

This project uses the rag-th compliance framework for RAG auditing.

- Framework docs: audit/docs/framework/
- Domain checklist: audit/docs/domains/legal/thai-legal-rag-audit-checklist.md
- When asked to audit, read the relevant checklist and score each control
  against this project's actual implementation.
```

### ตัวอย่าง Prompt

เมื่อ setup เสร็จแล้ว สั่ง Claude Code ได้เลย:

**Audit แบบเต็ม**
```
Audit this RAG system against the Thai legal checklist.
Read audit/docs/domains/legal/thai-legal-rag-audit-checklist.md
and score each control against our codebase.
```

**Audit เฉพาะด้าน**
```
Check our ingestion pipeline against audit/docs/framework/02-ingestion-pipeline.md.
Do we have metadata validation, PII scanning, and structured chunking?
```

**ตรวจ Security**
```
Review this project for RAG security threats listed in
audit/docs/framework/04-security.md. Focus on prompt injection
and data exfiltration risks.
```

**วิเคราะห์ช่องว่าง (Gap Analysis)**
```
Compare our current implementation against the framework in audit/docs/framework/.
List what we have, what's missing, and what needs improvement.
```

**สร้าง Audit Report**
```
Run a full audit using the Thai legal checklist.
Output a YAML report with control_id, status (PASS/PARTIAL/FAIL),
finding, and remediation for each control.
```

### Claude Code จะทำอะไร

เมื่อสั่ง audit Claude Code จะ:

1. อ่าน framework docs หรือ domain checklist ที่เกี่ยวข้อง
2. สำรวจ code, config และ documentation ของ project
3. เทียบ implementation กับแต่ละ control
4. ให้คะแนนแต่ละ control เป็น `PASS` / `PARTIAL` / `FAIL` / `N/A`
5. สร้าง audit report พร้อม findings และข้อแนะนำ

### เคล็ดลับ

- เริ่มจาก **audit เฉพาะด้าน** (framework doc เดียว) ก่อนยิง checklist เต็ม
- สำหรับ project ใหญ่ audit ทีละหมวด เช่น "audit category D: PDPA controls"
- สั่งให้ output เป็น YAML หรือ markdown table เพื่อง่ายต่อการติดตาม
- หลังแก้ไขแล้ว audit ซ้ำเพื่อยืนยันว่า remediation ผ่าน

## เริ่มต้นใช้งาน

1. อ่าน [framework docs](docs/framework/) เพื่อเข้าใจ compliance baseline
2. เลือกหรือสร้าง [domain checklist](docs/domains/)
3. ติดตั้ง Claude Code integration (ดูด้านบน)
4. รัน audit ครั้งแรก
5. แก้ไข findings แล้ว audit ซ้ำ

## Contributing

- เพิ่ม domain checklist ใหม่ใน `docs/domains/<domain>/`
- ปรับปรุง generic framework ใน `docs/framework/`
- แชร์ประสบการณ์ audit และ edge cases ที่เจอ

## License

MIT
