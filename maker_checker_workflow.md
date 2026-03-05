# Maker–Checker Workflow for M&A Legal Ontology

This workflow maps each ontology step into a **Maker agent** (does the work) and a **Checker agent** (independently validates output before handoff).

## Operating pattern for every step

1. **Maker** produces draft output + evidence bundle (sources, assumptions, open issues).
2. **Checker** runs a structured review against acceptance criteria.
3. **Decision**: `Approve` / `Approve with comments` / `Reject and rework`.
4. **Audit log** records prompt version, documents used, outputs, and sign-off.

---

## End-to-end workflow

| # | Ontology step | Maker agent | Checker agent | Key inputs | Deliverable | Checker gates |
|---|---|---|---|---|---|---|
| 1 | Engagement kicked-off post LOI | **Engagement Setup Maker** | **Engagement Setup Checker** | LOI, deal metadata, parties | Matter workspace, timeline, responsibilities matrix | Correct deal metadata, owner assignments complete, deadlines captured |
| 2 | Generate due diligence request list | **DD Request List Maker** | **DD Scope Checker** | LOI, deal type, jurisdiction templates | Prioritized DD request list | Coverage across corporate/commercial/employment/IP/regulatory/tax/litigation; no critical category missing |
| 3 | Ingest all documents from data room | **Data Room Ingestion Maker** | **Ingestion QC Checker** | Data room export, folder map | Indexed corpus with doc IDs, metadata, OCR status | No corrupt files, duplicates flagged, naming normalized, % OCR success threshold met |
| 4 | Review contracts & agreements | **Contract Review Maker** | **Issue Spotting Checker** | Ingested contracts, risk playbook | Clause-level extraction + risk tags | Material terms extracted, risk ratings justified, citations traceable to source text |
| 5 | Flag missing docs | **Missing Docs Maker** | **Completeness Checker** | DD request list, ingested corpus | Gap report with owner and due dates | Every request item mapped to Found/Missing/Partial with evidence |
| 6 | Draft due diligence memo | **DD Memo Maker** | **DD Memo Checker** | Review findings, gap report | DD memo draft | Risks ranked, deal impact clear, factual statements citation-backed |
| 7 | Client due diligence call | **Client Call Prep Maker** | **Client Comms Checker** | DD memo, open questions | Call agenda, talking points, decision log template | Advice clarity, decision points explicit, unresolved items tracked |
| 8 | Pull relevant precedent | **Precedent Retrieval Maker** | **Precedent Fit Checker** | Client positions, issue list, precedent bank | Precedent pack with rationale | Jurisdiction/transaction fit, clause relevance, latest approved precedent only |
| 9 | Draft definitive agreements | **Definitive Drafting Maker** | **Draft Quality Checker** | Precedent pack, commercial terms | Draft SPA/APA + schedules | Definitions consistency, fallback protections preserved, unresolved placeholders listed |
| 10 | Draft ancillary agreements | **Ancillary Drafting Maker** | **Ancillary Checker** | Main agreement draft, required ancillaries list | Employment, TSA, escrow, transition docs as needed | Cross-document consistency, references and exhibit links valid |
| 11 | Partner reviews draft (loop) | **Partner Review Synthesizer Maker** | **Partner Review QA Checker** | Partner comments, current drafts | Consolidated action list + revised drafts | All partner comments dispositioned (accepted/rejected/reason) |
| 12 | Send draft to counterparty | **Outbound Package Maker** | **Transmission Checker** | Approved draft set, cover note | Counterparty package + version tag | Correct version sent, redline baseline locked, privilege/confidentiality checks done |
| 13 | Receive markup from counterparty | **Markup Intake Maker** | **Markup Integrity Checker** | Counterparty redlines, comment files | Parsed markup summary | All changes parsed, unknown/formatting losses flagged |
| 14 | Analyze redlines | **Redline Analysis Maker** | **Negotiation Risk Checker** | Parsed markup, fallback matrix | Clause-by-clause accept/pushback recommendations | Position aligns to mandate, risk escalation thresholds applied |
| 15 | Coordinate specialist input | **Specialist Routing Maker** | **Specialist Coverage Checker** | Redline analysis, specialty triggers | Tax/IP/Reg/Employment question packs | All specialty-triggered issues routed with deadlines and context |
| 16 | Partner reviews issues with client on a call | **Issue Briefing Maker** | **Client Position Checker** | Specialist feedback, recommendation matrix | Client decision deck + mandate updates | Client instructions captured unambiguously, authority limits documented |
| 17 | Build issues list and response positions | **Response Matrix Maker** | **Response Consistency Checker** | Client mandate, partner guidance | Negotiation issues tracker | Every issue has owner, position, fallback, rationale, dependency |
| 18 | Negotiation call (loop) | **Live Negotiation Support Maker** | **Call Record Checker** | Response matrix, live notes | Updated decisions + agreed wording log | Real-time updates accurate; unresolved points explicitly parked |
| 19 | Update disclosure schedule | **Disclosure Update Maker** | **Disclosure Accuracy Checker** | Negotiated outcomes, disclosure data | Updated disclosure schedules | Schedule items tie to agreement reps/warranties; no stale placeholders |
| 20 | Sign agreements | **Signing Packet Maker** | **Execution Formalities Checker** | Final docs, signer authorities | Signature-ready set + execution memo | Correct entity/signatory blocks, dating, signing order, authority evidence |
| 21 | Generate closing checklist | **Closing Checklist Maker** | **Closing Completeness Checker** | Conditions precedent/subsequent, signed docs | Dynamic closing checklist | Every condition has status, evidence owner, target completion date |
| 22 | Prepare board resolutions | **Resolutions Drafting Maker** | **Corporate Formalities Checker** | Corporate authorities, transaction docs | Board/shareholder resolutions | Corporate law compliance, approvals match transaction steps |
| 23 | Prepare signature packets | **Packet Assembly Maker** | **Packet Integrity Checker** | Final forms, execution plan | Packets by signer/entity | Correct docs per signer, no missing pages/exhibits, naming/versioning clean |
| 24 | Closing call | **Closing Orchestrator Maker** | **Closing QA Checker** | Closing checklist, packets, funds flow | Closing minutes + completion log | Conditions satisfied before release; timestamped completion evidence |
| 25 | Post-closing filings | **Post-Closing Filing Maker** | **Filing Compliance Checker** | Closing outputs, statutory requirements | Filed forms + docket tracker | Filing deadlines met, receipt evidence stored, residual obligations diarized |

---

## Loop controls from ontology

1. **Drafting loop**: Steps 9–11 iterate until Partner Review QA Checker returns `Approve`.
2. **Negotiation loop**: Steps 13–18 iterate until all open issues are either resolved or explicitly accepted as residual risk.
3. **DD clarification loop**: Steps 6–8 may repeat when client call changes risk tolerance or requested protections.

---

## Agent contract (recommended JSON schema)

Use the same schema across all maker/checker agents to make orchestration simple:

```json
{
  "step_id": "15",
  "step_name": "Coordinate specialist input",
  "maker_output": {
    "summary": "...",
    "artifacts": ["tax_questions_v3.docx", "ip_issue_pack.md"],
    "citations": [{"doc_id": "DR-204", "location": "p.14"}],
    "open_questions": ["..."],
    "risk_level": "medium"
  },
  "checker_review": {
    "status": "approve_with_comments",
    "findings": ["employment trigger not routed"],
    "required_fixes": ["add specialist brief for non-compete enforceability"],
    "score": 0.86
  },
  "audit": {
    "maker_agent_version": "v1.3.2",
    "checker_agent_version": "v1.1.0",
    "timestamp_utc": "2026-03-05T10:45:00Z"
  }
}
```

---

## Minimal orchestration design

- **State machine**: `NotStarted -> MakerInProgress -> CheckerReview -> Rework (if rejected) -> Approved -> NextStep`.
- **Escalation policy**: Auto-escalate to human partner when checker score < threshold on high-risk clauses (e.g., liability cap, indemnity, termination, regulatory approvals).
- **SLA targets**:
  - Maker first pass: 5–20 min depending on step.
  - Checker turnaround: 2–10 min.
  - Rework cycle cap: 2 auto-rework rounds before human review.
- **Quality KPIs**:
  - First-pass checker approval rate.
  - Hallucination/citation defect rate.
  - Negotiation cycle count per issue category.
  - Missed deadline rate for filings and conditions precedent.

