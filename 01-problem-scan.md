# Phase 1 — SCAN

## 1. Danh sách các bài toán vận hành

| # | Subsidiary            | Lens                                           | Mô tả ngắn bài toán                                                                                                                  |
| - | --------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| 1 | Vinhomes              | Time-consuming + AI-upgrade + Stakeholder Pain | Nhân viên CSKH phải đọc, phân loại và soạn phản hồi cho số lượng lớn yêu cầu/khiếu nại của cư dân.                                   |
| 2 | VinFast               | Repetitive + Time-consuming                    | Nhân viên CSKH/kỹ thuật phải phân loại các yêu cầu hỗ trợ liên quan đến xe điện, lỗi xe và pin trước khi chuyển đến bộ phận phù hợp. |
| 3 | Xanh SM               | Repetitive + Stakeholder Pain                  | Tài xế gặp tình huống điểm đón không thuận tiện hoặc không chính xác, phải tự điều chỉnh điểm đón và liên hệ hỗ trợ khi cần.         |
| 4 | Vinmec                | Time-consuming + Repetitive                    | Nhân viên y tế phải tổng hợp và đọc nhiều thông tin từ hồ sơ bệnh án để có được một bản tóm tắt phục vụ quá trình khám/chăm sóc.     |
| 5 | Vinpearl / VinWonders | Repetitive + AI-upgrade                        | Nhân viên CSKH phải xử lý nhiều câu hỏi lặp lại của khách về vé, giờ hoạt động, dịch vụ, địa điểm và quy định sử dụng.               |

---

## Problem #1 — Vinhomes: Hỗ trợ xử lý yêu cầu và khiếu nại cư dân

### Pain point

Bộ phận chăm sóc khách hàng phải tiếp nhận nhiều yêu cầu từ cư dân qua các kênh khác nhau. Nhân viên cần đọc nội dung, xác định loại vấn đề, kiểm tra thông tin liên quan và soạn phản hồi phù hợp trước khi gửi.

Các yêu cầu có thể liên quan đến nhiều nhóm như:

* Dịch vụ và tiện ích của khu đô thị.
* Vệ sinh, an ninh, bãi đỗ xe.
* Bảo trì/sửa chữa.
* Phí dịch vụ.
* Phản ánh về chất lượng dịch vụ.
* Các yêu cầu cần chuyển sang bộ phận vận hành khác.

### Bottleneck

Phần tốn thời gian nhất là:

1. Đọc và hiểu nội dung yêu cầu tự nhiên của cư dân.
2. Phân loại yêu cầu.
3. Xác định bộ phận cần xử lý.
4. Soạn phản hồi phù hợp với chính sách.
5. Kiểm tra lại trước khi gửi.

### AI opportunity

LLM có thể hỗ trợ:

* Phân loại yêu cầu.
* Tóm tắt vấn đề.
* Trích xuất thông tin quan trọng.
* Đề xuất phản hồi dựa trên knowledge base/policy.
* Đề xuất routing đến bộ phận phù hợp.

Nhân viên vẫn duyệt trước khi phản hồi được gửi cho cư dân.

### Metric đề xuất

Mục tiêu ban đầu cần được xác minh bằng baseline thực tế:

> Giảm thời gian xử lý một yêu cầu từ khoảng 10 phút xuống dưới 2–3 phút đối với các yêu cầu thuộc nhóm đủ điều kiện tự động hỗ trợ.

Các metric bổ sung:

* ≥85% yêu cầu được phân loại đúng.
* ≥90% draft phản hồi không cần chỉnh sửa lớn.
* 0% phản hồi được gửi cho khách hàng mà không qua bước human review trong giai đoạn prototype.

---

## Problem #2 — VinFast: AI hỗ trợ chẩn đoán và điều phối xử lý sự cố xe

### Pain point

Khi xe gặp sự cố, khách hàng thường mô tả vấn đề bằng ngôn ngữ tự nhiên thay vì các thuật ngữ kỹ thuật, ví dụ: *"xe rung khi tăng tốc, đèn cảnh báo màu vàng sáng và hôm qua bắt đầu xuất hiện sau khi sạc"*.

Nhân viên CSKH hoặc kỹ thuật viên cần kết hợp nhiều nguồn thông tin như:

* Mô tả triệu chứng từ khách hàng.
* Model và phiên bản xe.
* Warning/error codes.
* Dữ liệu vận hành hoặc telemetry nếu có.
* Lịch sử bảo dưỡng và các sự cố trước đó.

Sau đó họ phải đánh giá nguyên nhân có khả năng xảy ra, mức độ nghiêm trọng và quyết định khách hàng nên tiếp tục sử dụng xe, đưa xe đến service center hay cần hỗ trợ roadside assistance.

### Bottleneck

Các bước tốn thời gian và yêu cầu nhiều kinh nghiệm:

1. Đọc và hiểu mô tả triệu chứng không có cấu trúc.
2. Xác định các triệu chứng và thông tin kỹ thuật quan trọng.
3. Đối chiếu error code/telemetry với knowledge base.
4. Xem xét lịch sử lỗi và bảo dưỡng của xe.
5. Đánh giá mức độ nghiêm trọng.
6. Đề xuất hướng xử lý hoặc chuyển đến bộ phận kỹ thuật phù hợp.

Đặc biệt, cùng một triệu chứng có thể xuất phát từ nhiều nguyên nhân khác nhau, khiến việc chẩn đoán ban đầu không đơn giản bằng rule-based classification.

### AI opportunity

AI Vehicle Diagnostic Copilot có thể hỗ trợ:

* Phân tích mô tả triệu chứng bằng ngôn ngữ tự nhiên.
* Trích xuất các triệu chứng và thông tin kỹ thuật quan trọng.
* Kết hợp triệu chứng với error codes, telemetry và lịch sử xe.
* Tra cứu knowledge base về các lỗi tương tự.
* Đưa ra một số **possible causes** thay vì khẳng định một nguyên nhân duy nhất.
* Đánh giá severity và confidence.
* Đề xuất hành động tiếp theo.
* Tạo bản tóm tắt có cấu trúc cho kỹ thuật viên.

AI **không được tự ý kết luận xe chắc chắn bị lỗi gì hoặc tự động thực hiện hành động liên quan đến an toàn**.

Các trường hợp confidence thấp, có dấu hiệu nguy hiểm hoặc liên quan đến hệ thống quan trọng phải được chuyển cho kỹ thuật viên/người có chuyên môn.

### Metric đề xuất

Mục tiêu ban đầu cần được xác minh bằng dữ liệu thực tế:

> Giảm thời gian từ khi tiếp nhận thông tin sự cố đến khi có **initial diagnostic assessment** từ khoảng 10 phút xuống dưới 2–3 phút đối với các trường hợp đủ dữ liệu.

Các metric bổ sung:

* ≥85% triệu chứng chính được AI trích xuất chính xác.
* ≥80% trường hợp được đưa vào đúng nhóm severity.
* ≥90% bản tóm tắt diagnostic được kỹ thuật viên đánh giá là hữu ích.
* 100% trường hợp có severity cao hoặc confidence thấp được chuyển sang human review.
* 0 quyết định liên quan đến an toàn được thực hiện hoàn toàn tự động trong giai đoạn prototype.

Các con số trên là target để thiết kế prototype và cần được xác minh bằng dữ liệu production.

---

## Problem #3 — Xanh SM: Điểm đón không tối ưu

### Pain point

Trong một số tình huống, điểm đón do hệ thống đề xuất có thể không thuận tiện cho tài xế hoặc hành khách do đặc điểm đường sá, vị trí thực tế hoặc điều kiện tại thời điểm đón.

Tài xế có thể phải tự điều chỉnh điểm đón hoặc liên hệ hỗ trợ.

### Bottleneck

Các vấn đề chính:

* Điểm đón không phù hợp với vị trí thực tế.
* Tài xế phải mất thời gian xác nhận/điều chỉnh.
* Khách hàng và tài xế có thể phải trao đổi nhiều lần.
* Có thể làm tăng thời gian chờ.

### AI opportunity

Hệ thống có thể sử dụng dữ liệu lịch sử chuyến đi và thông tin vị trí để:

* Phát hiện các khu vực thường có vấn đề về điểm đón.
* Đề xuất điểm đón thay thế.
* Phát hiện các trường hợp bất thường.

Tuy nhiên, việc lựa chọn điểm đón cuối cùng cần tuân thủ các ràng buộc giao thông và an toàn.

### Metric đề xuất

> Giảm tỷ lệ chuyến phải điều chỉnh điểm đón thủ công ít nhất 20% so với baseline.

Các metric bổ sung:

* Thời gian chờ trung bình của khách.
* Tỷ lệ tài xế phải liên hệ hỗ trợ.
* Tỷ lệ đề xuất điểm đón được tài xế chấp nhận.

---

## Problem #4 — Vinmec: Tóm tắt hồ sơ bệnh án

### Pain point

Nhân viên y tế có thể phải xem nhiều thông tin từ hồ sơ bệnh án để nhanh chóng nắm được tình trạng và lịch sử của bệnh nhân.

Việc tổng hợp thông tin thủ công có thể tốn thời gian, đặc biệt khi dữ liệu nằm ở nhiều phần khác nhau của hồ sơ.

### Bottleneck

Các bước tốn thời gian:

* Tìm thông tin liên quan.
* Đọc các ghi chú trước đó.
* Tổng hợp lịch sử.
* Xác định các thông tin quan trọng.

### AI opportunity

LLM có thể hỗ trợ tạo bản tóm tắt có cấu trúc từ dữ liệu được phép sử dụng:

* Lý do khám.
* Tiền sử liên quan.
* Kết quả xét nghiệm.
* Thuốc đang sử dụng.
* Các sự kiện y tế gần đây.

Bản tóm tắt chỉ là công cụ hỗ trợ. Bác sĩ/nhân viên y tế phải kiểm tra nội dung trước khi sử dụng.

### Metric đề xuất

> Giảm thời gian tạo bản tóm tắt hồ sơ từ khoảng 10 phút xuống dưới 2 phút đối với các hồ sơ phù hợp.

Ngoài thời gian, cần đánh giá:

* Độ đầy đủ thông tin.
* Độ chính xác.
* Tỷ lệ hallucination.
* Tỷ lệ bản tóm tắt được bác sĩ chấp nhận.

---

## Problem #5 — Vinpearl / VinWonders: Xử lý câu hỏi khách hàng

### Pain point

Khách hàng thường hỏi các thông tin có tính lặp lại như:

* Giờ mở cửa.
* Giá/vé.
* Quy định sử dụng.
* Địa điểm các tiện ích.
* Dịch vụ đi kèm.
* Cách sử dụng hoặc đặt dịch vụ.

Nhân viên CSKH phải trả lời nhiều câu hỏi tương tự.

### Bottleneck

Các câu hỏi lặp lại chiếm thời gian của nhân viên CSKH và có thể dẫn đến thời gian phản hồi không đồng đều.

### AI opportunity

LLM kết hợp với knowledge base có thể:

* Hiểu câu hỏi tự nhiên.
* Tìm thông tin liên quan.
* Tạo câu trả lời dễ hiểu.
* Đề xuất chuyển nhân viên khi câu hỏi nằm ngoài phạm vi.

### Metric đề xuất

> ≥85% câu hỏi thuộc nhóm FAQ được trả lời chính xác mà không cần nhân viên soạn lại từ đầu.

Mục tiêu tốc độ:

> Giảm thời gian phản hồi các câu hỏi FAQ xuống dưới 10 giây.

Đối với các vấn đề liên quan đến hoàn tiền, khiếu nại hoặc chính sách ngoại lệ, hệ thống phải chuyển sang nhân viên.

---
# 🃏 Phase 2 — QUICK-ASSESS

## QUICK PROBLEM CARD #1

**Bài toán (1 câu):**
Nhân viên CSKH Vinhomes mất nhiều thời gian đọc, phân loại và soạn phản hồi cho các yêu cầu/khiếu nại của cư dân.

**Công ty thành viên:**
[ ] VinFast  [ ] Xanh SM  [x] Vinhomes
[ ] Vinmec  [ ] Khác (Ghi rõ): __________

**Ai đang đau (Actor)?**
Nhân viên chăm sóc khách hàng Vinhomes.

**Workflow thủ công hiện tại (3–5 bước):**

1. Nhận yêu cầu từ cư dân → 2. Đọc và hiểu nội dung → 3. Phân loại yêu cầu → 4. Tra cứu thông tin và soạn phản hồi → 5. Kiểm tra và gửi phản hồi.

**Bước nào tốn thời gian/lỗi nhất?**
Đọc, phân loại và soạn phản hồi **(⏱ khoảng 10 phút/lượt)**.

**AI có thể nhảy vào hỗ trợ ở bước nào?**
Bước 2–4: tóm tắt yêu cầu, phân loại, trích xuất thông tin, tra cứu policy và tạo draft phản hồi.

**Đo thành công bằng gì (Metric có số)?**
Giảm thời gian xử lý từ **10 phút → dưới 2–3 phút/lượt**, với **≥85%** yêu cầu được phân loại đúng.

**Quick Architecture:**
[ ] No AI  [ ] Rule  [x] LLM  [ ] Agent

---

## QUICK PROBLEM CARD #2

**Bài toán (1 câu):**

Kỹ thuật viên/CSKH VinFast mất thời gian phân tích mô tả triệu chứng, mã lỗi và dữ liệu xe để đánh giá sự cố và xác định hướng xử lý ban đầu.

**Công ty thành viên:**

[x] VinFast  [ ] Xanh SM  [ ] Vinhomes

[ ] Vinmec  [ ] Khác (Ghi rõ): __________

**Ai đang đau (Actor)?**

Nhân viên CSKH và kỹ thuật viên chẩn đoán xe.

**Workflow thủ công hiện tại (3–5 bước):**

1. Nhận mô tả sự cố từ khách hàng → 2. Đọc và xác định triệu chứng → 3. Kiểm tra error code/telemetry/lịch sử xe → 4. Đối chiếu knowledge base và đánh giá nguyên nhân → 5. Xác định mức độ nghiêm trọng và hướng xử lý.

**Bước nào tốn thời gian/lỗi nhất?**

Đối chiếu nhiều nguồn thông tin để đánh giá nguyên nhân và mức độ nghiêm trọng **(⏱ khoảng 10 phút/lượt)**.

**AI có thể nhảy vào hỗ trợ ở bước nào?**

Bước 2–5: trích xuất triệu chứng, phân tích error code, tra cứu knowledge base, đối chiếu lịch sử xe, đưa ra possible causes, severity và đề xuất action.

**Đo thành công bằng gì (Metric có số)?**

Giảm thời gian initial diagnostic assessment từ **10 phút → dưới 2–3 phút/lượt**, với **≥85% triệu chứng chính được trích xuất đúng** và **100% trường hợp severity cao/confidence thấp được chuyển sang human review**.

**Quick Architecture:**

[ ] No AI  [ ] Rule  [ ] LLM  [x] Agent

---

## QUICK PROBLEM CARD #3

**Bài toán (1 câu):**
Nhân viên CSKH Vinpearl/VinWonders mất thời gian trả lời các câu hỏi lặp lại của khách hàng về vé, giờ hoạt động và dịch vụ.

**Công ty thành viên:**
[ ] VinFast  [ ] Xanh SM  [ ] Vinhomes
[ ] Vinmec  [x] Khác (Ghi rõ): Vinpearl / VinWonders

**Ai đang đau (Actor)?**
Nhân viên chăm sóc khách hàng.

**Workflow thủ công hiện tại (3–5 bước):**

1. Nhận câu hỏi từ khách → 2. Đọc và xác định chủ đề → 3. Tra cứu thông tin/policy → 4. Soạn câu trả lời → 5. Gửi cho khách hàng.

**Bước nào tốn thời gian/lỗi nhất?**
Tra cứu thông tin và soạn câu trả lời **(⏱ khoảng 3–5 phút/lượt)**.

**AI có thể nhảy vào hỗ trợ ở bước nào?**
Bước 2–4: xác định intent, tìm thông tin trong knowledge base và tạo draft câu trả lời.

**Đo thành công bằng gì (Metric có số)?**
Đạt **≥85% câu hỏi FAQ được trả lời chính xác** và giảm thời gian phản hồi từ **3–5 phút → dưới 10 giây**.

**Quick Architecture:**
[ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
