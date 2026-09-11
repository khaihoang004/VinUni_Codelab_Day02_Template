# 01 - Problem Scan: Supply Chain (VinFast)

Ngày: 2026-09-11

## Tóm tắt nhanh
Chủ đề chọn: Quản lý chuỗi cung ứng sản xuất tại VinFast — giảm rủi ro gián đoạn do biến động lead-time nhà cung cấp và thiếu hụt linh kiện.

## 1) Danh sách 5 bài toán (theo 4 lenses)
| # | Subsidiary | Lens | Mô tả ngắn |
|---|------------|------|------------|
| 1 | VinFast | Time-consuming / Repetitive | Dự báo nhu cầu linh kiện phụ tùng (demand forecasting) không chính xác dẫn tới tồn kho không tối ưu, thiếu hụt làm ngưng dây chuyền. |
| 2 | VinFast | Stakeholder Pain | Biến động lead-time nhà cung cấp (variable lead-times) gây kế hoạch sản xuất bị lệch, phải chạy overtime hoặc hoãn lô xuất. |
| 3 | VinFast | AI-upgrade | Kiểm toán chất lượng dữ liệu đơn hàng & PO thủ công tốn thời gian, nhiều lỗi nhập liệu giữa ERP và hệ thống SCM. |
| 4 | VinFast | Repetitive | Ưu tiên phân bổ linh kiện khi thiếu (allocation) được xử lý thủ công theo rule tĩnh, thiếu tối ưu theo ảnh hưởng sản xuất. |
| 5 | VinFast | Time-consuming | Phát hiện sớm rủi ro đứt gãy chuỗi cung ứng (supplier risk) dựa trên nhiều nguồn data (lead time, lịch sử giao, chất lượng) hiện làm bằng báo cáo thủ công. |

## 2) Top 3 Quick Problem Cards

---
QUICK PROBLEM CARD #1

Bài toán (1 câu): Dự báo nhu cầu linh kiện chính xác hơn để giảm thiếu hụt và chi phí tồn kho.

Công ty thành viên: VinFast

Ai đang đau (Actor)? Planner sản xuất, Supply Planner, Inventory Manager.

Workflow thủ công hiện tại (3-5 bước):
	1. Lấy forecast lịch sử + đơn hàng bán hiện có
	2. Tính nhu cầu thô bằng Excel/ERP
	3. Sửa tay theo judgment của planner
	4. Tạo PO gửi nhà cung cấp

Bước nào tốn thời gian/lỗi nhất? Bước 3 (manual adjustment) — ~30-120 phút/lượt

AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3: gợi ý forecast demand, đề xuất adjustments và confidence interval.

Đo thành công bằng gì (Metric có số)? Giảm % stockout hàng critical từ 8% → ≤2%; giảm inventory holding cost 10%.

Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent

---
QUICK PROBLEM CARD #2

Bài toán (1 câu): Tự động dự đoán và điều chỉnh kế hoạch khi lead-time nhà cung cấp biến động.

Ai đang đau (Actor)? Production Scheduler, Procurement Manager.

Workflow thủ công hiện tại (3-5 bước):
	1. Nhà cung cấp báo lead-time (email/portal)
	2. Planner cập nhật ERP thủ công
	3. Scheduler kiểm tra tác động lên plan, họp nhanh quyết định
	4. Thực hiện biện pháp (quay vòng, rush order)

Bước nào tốn thời gian/lỗi nhất? Bước 2-3: Valiation & replan — ~60-180 phút/lượt

AI có thể nhảy vào hỗ trợ ở bước nào? Tự động ingest thông báo lead-time, phân tích impact, recommend replan và urgency.

Đo thành công bằng gì (Metric có số)? Giảm thời gian replan từ trung bình 90 phút xuống ≤15 phút; giảm lô sản xuất bị ảnh hưởng 70% → ≤20%.

Quick Architecture: [ ] No AI  [ ] Rule  [ ] LLM  [x] Agent

---
QUICK PROBLEM CARD #3

Bài toán (1 câu): Phân bổ linh kiện ưu tiên cho các lô sản xuất theo KPI sản xuất và rủi ro thiếu hụt.

Ai đang đau (Actor)? Inventory Controller, Line Manager.

Workflow thủ công hiện tại (3-5 bước):
	1. Khi thiếu, Planner liệt kê demand backlog
	2. Quyết định ưu tiên bằng rule tĩnh (FIFO hoặc theo đơn hàng khách hàng)
	3. Thực hiện chuyển phân bổ

Bước nào tốn thời gian/lỗi nhất? Bước 2: quyết định ưu tiên — ~20-60 phút/lượt, thiếu tính ảnh hưởng KPI.

AI có thể nhảy vào hỗ trợ ở bước nào? Tính toán impact-based allocation bằng mô phỏng tối ưu, đề xuất allocation với explainability.

Đo thành công bằng gì (Metric có số)? Tăng throughput line-critical 5% và giảm OT 15%.

Quick Architecture: [ ] No AI  [x] LLM  [ ] Agent

---

## Ghi chú
Chọn ý tưởng để triển khai Deep-Dive: QUICK PROBLEM CARD #2 — "Dự đoán và điều chỉnh kế hoạch khi lead-time nhà cung cấp biến động" (số 2). Đây là điểm đau có tính lặp lại, ảnh hưởng trực tiếp tới tiến độ sản xuất và chi phí.

