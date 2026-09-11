# Phase 3 — DEEP-DIVE

## Problem Statement

### 1. Actor / Operator

**Nhân viên CSKH và kỹ thuật viên chẩn đoán xe VinFast.**

CSKH là người tiếp nhận mô tả sự cố từ khách hàng. Kỹ thuật viên chịu trách nhiệm phân tích triệu chứng, error code, dữ liệu xe và lịch sử bảo dưỡng để đưa ra initial diagnostic assessment.

---

### 2. Current Workflow

Quy trình hiện tại:

**1. Khách hàng báo sự cố**
↓
**2. CSKH tiếp nhận và đọc mô tả**
↓
**3. Xác định các triệu chứng chính**
↓
**4. Kiểm tra error code / telemetry / thông tin xe**
↓
**5. Tra cứu tài liệu kỹ thuật / knowledge base**
↓
**6. Kiểm tra lịch sử bảo dưỡng và các lỗi trước đó**
↓
**7. Kỹ thuật viên đánh giá nguyên nhân có khả năng xảy ra và mức độ nghiêm trọng**
↓
**8. Đề xuất hướng xử lý**

Thông tin đầu vào thường không có cấu trúc và có thể nằm ở nhiều nguồn khác nhau.

---

### 3. Bottleneck

Bottleneck chính là **tổng hợp và đối chiếu nhiều nguồn thông tin để đưa ra đánh giá ban đầu về sự cố**.

Các vấn đề chính:

* Khách hàng mô tả triệu chứng bằng ngôn ngữ tự nhiên, không theo format kỹ thuật.
* Một triệu chứng có thể liên quan đến nhiều nguyên nhân.
* Kỹ thuật viên phải tra cứu nhiều nguồn thông tin.
* Phải kết hợp error code, telemetry và lịch sử xe.
* Việc đánh giá severity phụ thuộc nhiều vào kinh nghiệm của kỹ thuật viên.
* Các case phức tạp có thể phải chuyển qua nhiều cấp xử lý.

Thời gian initial diagnostic assessment được đặt làm baseline giả định khoảng **10 phút/case**, cần được xác minh bằng dữ liệu thực tế.

---

### 4. Business Impact

Quy trình thủ công có thể gây:

* Tăng thời gian khách hàng phải chờ hỗ trợ.
* Tăng workload cho CSKH và kỹ thuật viên.
* Chậm điều phối service center hoặc roadside assistance.
* Tăng số lượng case cần xử lý thủ công.
* Giảm khả năng mở rộng hệ thống hỗ trợ khi số lượng xe tăng.
* Có nguy cơ bỏ sót hoặc đánh giá chậm các trường hợp có mức độ nghiêm trọng cao.

Đối với các vấn đề liên quan đến an toàn xe, việc đánh giá sai hoặc chậm còn có thể tạo ra rủi ro lớn hơn cho khách hàng.

---

### 5. Success Metric

Mục tiêu prototype:

* Giảm thời gian initial diagnostic assessment từ **~10 phút xuống dưới 2–3 phút/case** đối với các case đủ dữ liệu và nằm trong phạm vi hỗ trợ.
* **≥85%** triệu chứng chính được AI trích xuất chính xác.
* **≥80%** case được đánh giá đúng severity.
* **≥90%** bản diagnostic summary được kỹ thuật viên đánh giá là hữu ích.
* **100%** case có severity cao hoặc confidence thấp được chuyển sang Human Review.
* **0** quyết định liên quan đến an toàn được thực hiện hoàn toàn tự động trong prototype.

Các baseline và target trên cần được kiểm chứng bằng dữ liệu production trước khi triển khai thực tế.

---

### 6. Operational Boundary

AI chỉ đóng vai trò **Vehicle Diagnostic Copilot**, hỗ trợ initial diagnostic assessment.

AI được phép:

* Phân tích mô tả triệu chứng.
* Trích xuất thông tin kỹ thuật.
* Tra cứu knowledge base.
* Đối chiếu error code và dữ liệu xe được cung cấp.
* Tổng hợp lịch sử lỗi/bảo dưỡng.
* Đưa ra possible causes.
* Đề xuất severity và confidence.
* Đề xuất hướng xử lý.

AI **không được**:

* Khẳng định chắc chắn một diagnosis khi chưa đủ bằng chứng.
* Tự động thực hiện thao tác trên xe.
* Tự động đưa ra quyết định liên quan đến an toàn.
* Tự động quyết định roadside assistance trong trường hợp chưa qua cơ chế kiểm soát.
* Thay thế kỹ thuật viên trong quyết định cuối cùng.

Các case có severity cao, confidence thấp, dữ liệu thiếu hoặc evidence mâu thuẫn phải được chuyển sang Human Review.

---

# Future-State Flow & AI Fit

## Future-State Flow

**Khách hàng gửi thông tin sự cố**
↓
**AI phân tích và chuẩn hóa triệu chứng**
↓
**AI lấy thông tin xe / error code / telemetry**
↓
**AI truy vấn Diagnostic Knowledge Base**
↓
**AI kiểm tra lịch sử xe**
↓
**AI tổng hợp evidence**
↓
**AI đưa ra possible causes + severity + confidence**
↓
**Human Technician Review**
↓
**Quyết định hướng xử lý**
↓
**Service Center / Roadside Assistance / Monitoring**

---

## Rule / State-Machine

Rule-based layer được sử dụng để kiểm soát các điều kiện rõ ràng và các safety boundary.

Ví dụ:

```text
IF critical_warning = TRUE
    → Mandatory Human Review

IF severity = HIGH
    → Mandatory Human Review

IF confidence < threshold
    → Human Review

IF required_vehicle_data is missing
    → Request additional information

IF evidence is contradictory
    → Human Review

IF diagnostic_evidence is insufficient
    → Do not provide definitive diagnosis
```

Rule engine không thực hiện diagnosis phức tạp mà đóng vai trò **control and safety layer** cho hệ thống AI.

---

## LLM Feature

LLM được sử dụng cho các tác vụ cần hiểu ngôn ngữ và tổng hợp thông tin:

* Hiểu mô tả triệu chứng từ khách hàng.
* Chuẩn hóa triệu chứng thành structured data.
* Trích xuất thông tin quan trọng.
* Tóm tắt lịch sử sự cố.
* Giải thích error code dựa trên knowledge base.
* Tổng hợp evidence từ nhiều nguồn.
* Tạo diagnostic summary.
* Đề xuất possible causes.

LLM output phải kèm theo **evidence và confidence**, thay vì chỉ trả về một diagnosis duy nhất.

---

## Agentic Loop

Agent chịu trách nhiệm điều phối quá trình thu thập và phân tích thông tin.

```text
Analyze Symptoms
      ↓
Get Vehicle Information
      ↓
Check Error Codes / Telemetry
      ↓
Search Diagnostic Knowledge Base
      ↓
Check Vehicle History
      ↓
Aggregate Evidence
      ↓
Evaluate Possible Causes
      ↓
Assess Severity
      ↓
Calculate Confidence
      ↓
Generate Recommendation
```

Nếu thông tin chưa đủ:

```text
Insufficient Evidence
        ↓
Request / Retrieve Additional Data
        ↓
Re-evaluate
```

Nếu confidence vẫn thấp:

```text
Low Confidence
        ↓
Human Technician Review
```

Agent chỉ được **đề xuất hành động**, không tự động thực hiện các hành động ảnh hưởng đến an toàn.

---

## Human-in-the-loop

Human Technician là bước bắt buộc trước khi đưa ra quyết định cuối cùng.

Kỹ thuật viên kiểm tra:

* Symptoms được AI trích xuất.
* Error codes và telemetry.
* Evidence từ knowledge base.
* Possible causes.
* Severity.
* Confidence.
* Recommended action.

Sau đó kỹ thuật viên quyết định:

* Tiếp tục sử dụng xe.
* Đưa xe đến service center.
* Yêu cầu roadside assistance.
* Thu thập thêm dữ liệu.
* Thực hiện quy trình kiểm tra kỹ thuật khác.

---

## Fallback

Hệ thống phải chuyển sang xử lý thủ công khi:

```text
confidence < threshold
OR
severity = HIGH
OR
critical_warning = TRUE
OR
required_data is missing
OR
evidence is contradictory
OR
case is outside knowledge base
```

Khi fallback xảy ra:

**AI không đưa ra definitive diagnosis → Human Technician xử lý case.**

---

# Phase 5 — EVALUATE

## Readiness Checklist

| Hạng mục                     | Đánh giá         | Nhận xét                                                                                                                         |
| ---------------------------- | ---------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| Có dữ liệu đầu vào phù hợp   | 🟡 Partial       | Có thể sử dụng symptom description, error code, telemetry và vehicle history, nhưng cần xác định dữ liệu thực tế có thể truy cập |
| Có Knowledge Base            | 🟡 Partial       | Cần có tài liệu kỹ thuật, error-code mapping và diagnostic procedures được kiểm duyệt                                            |
| Có historical data / logs    | 🟡 Partial       | Cần historical diagnostic cases để đánh giá chất lượng AI                                                                        |
| Có ground-truth labels       | 🔴 Chưa đủ       | Cần kỹ thuật viên gán nhãn severity, symptoms và kết quả diagnosis để đánh giá                                                   |
| Có metric đo lường           | 🟢 Có            | Diagnostic time, symptom extraction accuracy, severity accuracy, summary usefulness, escalation rate                             |
| Có Human-in-the-loop         | 🟢 Có            | Kỹ thuật viên xác nhận trước quyết định cuối cùng                                                                                |
| Có Fallback                  | 🟢 Có            | Case confidence thấp hoặc nguy hiểm được chuyển sang human                                                                       |
| Kiểm soát hallucination      | 🟡 Partial       | Cần grounding với knowledge base và yêu cầu AI cung cấp evidence                                                                 |
| Kiểm soát safety             | 🟢 Có            | Critical/high-severity case bắt buộc Human Review                                                                                |
| AI có quyền thực hiện action | 🟢 Được giới hạn | Agent chỉ đề xuất action, không tự động thực hiện thao tác liên quan đến an toàn                                                 |
| Stakeholder readiness        | 🟡 Partial       | Cần kỹ thuật viên và CSKH tham gia đánh giá prototype                                                                            |
| Có thể prototype             | 🟢 Có            | Có thể xây dựng prototype với Gemini + structured JSON + mock vehicle data                                                       |

---

## Risk Assessment

### Risk 1 — Hallucination

AI có thể đưa ra nguyên nhân không được hỗ trợ bởi dữ liệu.

**Mitigation:**

* Grounding với Diagnostic Knowledge Base.
* Yêu cầu evidence cho mỗi possible cause.
* Không cho phép AI đưa ra definitive diagnosis khi confidence thấp.

### Risk 2 — False Negative trong trường hợp nghiêm trọng

AI có thể đánh giá một case nguy hiểm ở severity thấp.

**Mitigation:**

* Critical warning → mandatory Human Review.
* Severity cao → mandatory Human Review.
* Rule-based safety layer độc lập với LLM.

### Risk 3 — Dữ liệu không đầy đủ

Customer input có thể không chứa đủ thông tin để chẩn đoán.

**Mitigation:**

* Agent yêu cầu thêm thông tin.
* Query thêm vehicle data nếu có.
* Fallback sang kỹ thuật viên nếu vẫn thiếu dữ liệu.

### Risk 4 — Knowledge Base lỗi thời

Diagnostic procedure hoặc error-code mapping có thể thay đổi.

**Mitigation:**

* Version hóa Knowledge Base.
* Chỉ sử dụng tài liệu đã được kiểm duyệt.
* Có quy trình cập nhật knowledge base.

---

# Final Decision

## 🟡 NOT YET

**Lý do:**

Bài toán có **AI fit tốt** và có thể xây dựng prototype tương đối rõ ràng. Agentic Loop có giá trị vì hệ thống cần thu thập và đối chiếu thông tin từ nhiều nguồn trước khi đưa ra initial diagnostic assessment.

Tuy nhiên, chưa nên triển khai production ngay vì:

1. Chưa có historical diagnostic dataset đủ lớn để đánh giá chính xác.
2. Chưa có ground-truth labels từ kỹ thuật viên.
3. Chưa xác định đầy đủ quyền truy cập vào telemetry, error codes và vehicle history.
4. Đây là bài toán liên quan đến **vehicle safety**, nên yêu cầu kiểm soát nghiêm ngặt hơn chatbot hoặc FAQ thông thường.
5. Cần validate hallucination, severity classification và false-negative rate trước khi đưa AI vào workflow thực tế.

### Prototype Recommendation

Có thể **GO cho prototype / controlled pilot**, nhưng **NOT YET cho production deployment**.

Prototype nên bắt đầu với:

**Mock Vehicle Data + Diagnostic Knowledge Base + Gemini LLM + Rule-based Safety Layer + Human-in-the-loop**

Sau khi prototype đạt các metric đề ra trên một tập test được kỹ thuật viên gán nhãn, có thể tiến tới controlled pilot với dữ liệu thực tế.
