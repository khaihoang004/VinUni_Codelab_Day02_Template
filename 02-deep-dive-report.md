# 02 - Deep Dive Report: Lead-time Variability Management (VinFast)

Ngày: 2026-09-11

## 0. Executive Summary
Vấn đề: Biến động lead-time nhà cung cấp làm lệch kế hoạch sản xuất, gây thiếu hụt linh kiện, tăng OT và chi phí rush orders. Đề xuất: hệ thống AI + agentic loop để tự động ingest thay đổi lead-time, phân tích impact, mô phỏng re-plan và đề xuất action cho human-in-the-loop (HITL). Quyết định: GO (MVP scoped).

## 1. Current-State Workflow Mapping
Current flow (tổng thời gian 90–240 phút/lượt khi xảy ra thay đổi):
1. Supplier thông báo thay đổi lead-time qua email/portal. (t0)
2. Procurement nhận, forward tới Planner; Planner kiểm tra PO liên quan. (t0 + 10–30p)
3. Planner cập nhật ERP/SCM (thủ công). (t0 + 30–90p)
4. Scheduler thực hiện impact analysis thủ công, họp nhanh, quyết định re-plan hoặc rush order. (t0 + 60–180p)
5. Thực thi điều chỉnh (rush shipment, prioritize lô) và cập nhật KPI. (t0 + 120–240p)

Bottlenecks:
- Manual ingestion & validation (bước 2–3) — lỗi nhập liệu và delay.
- Impact analysis & decision (bước 4) — tốn thời gian, thiếu simulation để ước lượng hậu quả chính xác.

## 2. Problem Statement (6-field)
1. Actor / Operator: Production Scheduler, Procurement Manager, Supply Planner.
2. Current Workflow: Như mô tả ở trên, phụ thuộc vào emails, spreadsheets và họp ad-hoc.
3. Bottleneck: Thời gian replan dài do phải validate thông tin và estimate impact thủ công; không có hệ thống cảnh báo tự động và mô phỏng tác động thời gian thực.
4. Business Impact: Trung bình 3% đơn hàng sản xuất bị delayed/tuần → tổn thất ≈ 500–2,000 CPU-hours sản xuất/tuần và chi phí rush orders ước tính 100k–300k USD/tháng cho một nhà máy lớn; ảnh hưởng uy tín giao hàng B2B.
5. Success Metric:
	 - Replan reaction time (time-to-recommend) giảm từ 90 phút → ≤15 phút.
	 - % lô sản xuất bị ảnh hưởng giảm từ 70% → ≤20% (trong scope tested scenarios).
	 - Precision of recommended action acceptance by human ≥ 85%.
6. Operational Boundary:
	 - AI chỉ được phép đề xuất re-plan và đánh giá impact; mọi thay đổi cuối cùng phải được con người phê duyệt (HITL).
	 - Tuyệt đối không tự động tạo PO emergency hoặc gửi lệnh sản xuất mà không có sign-off.

## 3. Root Causes & Data Availability
- Root causes: giao tiếp nhà cung cấp muộn/không chuẩn hoá; thiếu historical lead-time modeling; thiếu pipeline ingest data tự động.
- Data sources available: ERP PO history, ASN (Advance Shipping Notices), nhà cung cấp confirmations (email/portal logs), historical lead-times, production schedules, inventory levels, historical rush-order costs.

## 4. Future-State Flow & AI Fit
AI-fit: Agentic Loop (Agent coordinates ingestion → analysis → simulation → propose) + LLM for unstructured extraction (emails) + Rule-based simulation engine.

Future-State Flow (tự động):
1. [Blue 🔵 AI Step] Ingest message từ supplier (email/portal) → NER & extraction (lead-time, PO refs, qty).
2. [Blue 🔵 AI Step] Validate vs ERP (fuzzy match), compute delta lead-time.
3. [Blue 🔵 AI Step] Run fast simulation: recalc production plan impact (delay hours, downstream backlog, overtime need, cost delta).
4. [Green 🟢 Human Step HITL] Present ranked recommendations (e.g., prioritize lô A, expedite supplier X, merge shipments) kèm expected impact and confidence.
5. [Green 🟢 Human Step] Approve / modify / reject recommendation.
6. [Blue 🔵 AI Step] If approved, create task in MES/ERP for execution (but do not auto-execute PO/production changes without sign-off).
7. [↩️ Fallback] If extraction confidence < threshold or conflicting data, route to manual queue with highlighted fields.

## 5. Technical Design (MVP)
- Data ingestion: connector to supplier emails/portal, webhook/ETL to staging.
- Extraction & parsing: LLM prompt to extract structured fields (PO, lead-time, dates, quantity) + rule-based validators.
- Impact simulator: deterministic planner simulator (fast heuristics) to estimate delays and cost impact.
- Decision agent: orchestrator that calls extraction + simulator, ranks actions by cost/benefit, produces explainable JSON report.
- UI/HITL: Dashboard (tickets) showing recommended actions and allow approval.

## 6. Technical Prompt Prototype (brief)
- System Prompt (core directives):
	- Role: "You are an Operation Assistant for VinFast supply chain. Extract structured facts from supplier messages, only output JSON in the exact schema, and never fabricate PO numbers or dates. If uncertain, set confidence low and flag for human review."
- Structured Output JSON (schema):
	{
		"source_id": "...",
		"po_ref": "...",
		"supplier": "...",
		"reported_lead_time_days": 10,
		"previous_lead_time_days": 5,
		"delta_days": 5,
		"confidence": 0.92,
		"affected_pos": [{"po_ref":"...","qty":100,"eta":"YYYY-MM-DD"}],
		"recommended_actions": [{"action":"expedite","cost_estimate_usd":1200,"impact_hours":48}],
		"explanation":"..."
	}

## 7. Adversarial Testcases (MVP)
1. Supplier message with multiple PO refs and ambiguous dates — expect LLM to extract all PO refs and mark low confidence for ambiguous dates.
2. Malformed date formats or relative dates ("in 2 weeks") — expect normalized date or flag for review.
3. Conflicting info (supplier says shipped but ERP shows not shipped) — expect highlight conflict and route to manual queue.

## 8. Implementation Roadmap (MVP → Prod)
- Week 0–2: Data connectors + sample dataset (ERP, PO, ASN).
- Week 2–4: Build extraction pipeline + test on historical emails.
- Week 4–6: Implement fast simulator + action ranking.
- Week 6–8: Integrate HITL dashboard + user acceptance testing.
- Success criteria: meet Success Metrics on a held-out historical replay (A/B test on past incidents).

## 9. Evaluate — Decision
[x] GO (MVP scoped to a single plant, selected suppliers, with HITL required)

Justification: High business impact (production delays & rush costs), available historical data to train/validate models, mitigations via HITL keep operational risk low. The solution reduces time-to-recommend and enables faster, evidence-based re-planning.

## 10. Risks & Mitigations
- Hallucination/incorrect extraction → Mitigate: require confidence threshold, cross-validate with ERP.
- Data quality issues → Mitigate: initial scope on reliable suppliers & historical logs; add data cleaning stage.
- Stakeholder buy-in → Mitigate: start with reporting-only mode (read-only recommendations) for 2–4 weeks.

