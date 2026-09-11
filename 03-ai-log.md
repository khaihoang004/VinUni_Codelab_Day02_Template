# 03 - AI Log (Prompt Prototype & Reflection)

Ngày: 2026-09-11

## 1) Mục tiêu
Stress-test prompt prototype để:
- Trích xuất structured facts từ thông báo nhà cung cấp.
- Tính toán delta lead-time và mô phỏng impact.
- Đảm bảo ranh giới hoạt động (AI only recommends; human approves).

## 2) System Prompt (candidate)
You are an operations assistant for VinFast supply chain. Your job is to read supplier messages and output ONLY valid JSON following the schema: {po_ref, supplier, reported_lead_time_days, previous_lead_time_days, delta_days, confidence, affected_pos, recommended_actions, explanation}. Never invent PO numbers or dates. When uncertain, set "confidence" below 0.6 and include a short reason. Do not issue any commands to ERP/MES. Recommendation is advisory only.

## 3) Example User Prompt (for testing)
"Supplier ABC: 'We will delay PO VF-2023-998 by 7 days due to capacity constraints; ETA now 2026-10-05. See attached manifest.' Please extract structured info and recommend actions."

## 4) Structured Output Schema
{
	"source_id": "string",
	"po_ref": "string",
	"supplier": "string",
	"reported_lead_time_days": number,
	"previous_lead_time_days": number,
	"delta_days": number,
	"confidence": 0..1,
	"affected_pos": [{"po_ref":"string","qty":number,"eta":"YYYY-MM-DD"}],
	"recommended_actions": [{"action":"string","urgency":"low|med|high","cost_estimate_usd":number,"impact_hours":number}],
	"explanation":"string"
}

## 5) Adversarial Test Cases (prompts)
1. "We might ship late for multiple POs: VF-100, VF-101; dates fuzzy." (ambiguous PO mentions)
2. "We plan to push shipments by two fortnights." (relative date language)
3. "We shipped but carrier delayed; invoice says shipped on 2026/09/01 but tracking missing." (conflict with ERP)

## 6) Test Run Summary (simulated)
- Test 1 (ambiguous PO list): model extracted two PO refs, confidence 0.55 for dates → flagged for manual review. (expected)
- Test 2 (relative dates): model normalized to approximate date range, set confidence 0.48 → flagged. (expected)
- Test 3 (conflict): model detected mismatch vs ERP (simulated) and suggested human investigate, confidence 0.62.

## 7) Failures / Hallucination Observed (simulated & mitigations)
- Risk: model may fabricate exact dates when only relative language provided. Mitigation: require extraction policy to never output exact date when input ambiguous; instead output normalized range + low confidence.
- Risk: invented PO numbers (hallucination). Mitigation: cross-check extracted PO against ERP PO list; if missing, set confidence=0 and route to manual queue.

## 8) Changes made to prototype after tests
- Added: post-processing validator that checks extracted PO refs against ERP snapshot.
- Added: confidence threshold 0.6 to auto-route to HITL queue when lower.
- Added: explicit system instruction forbidding fabrication and requiring a "source_excerpt" field with the raw text snippet used for extraction.

## 9) Reflection (AI as thought-partner)
- What AI helped: Rapidly parsed unstructured supplier messages, enumerated impacted POs, and produced ranked recommendations with cost estimates — reduced human triage work in tests.
- What AI got wrong: Tendency to over-confidently normalize ambiguous temporal language and occasionally mismatch PO numbers when formatting differed. Cross-validation with ERP solved most cases.
- Next steps: integrate with live ERP snapshot, run historical-replay evaluation (A/B), and run limited pilot at one plant with HITL for 4 weeks.

---

End of AI Log

