# 01 - Problem Scan & Quick Cards

Học viên: Nguyễn Khắc Phi Long (branch PhiLong)
Vai trò: AI Product Engineer tại Vin Smart Future

Lưu ý: các con số về thời gian và khối lượng trong file này là ước lượng, cần xác nhận lại với số liệu vận hành thực tế trước khi triển khai.

## Phase 1 - Scan: 5 bài toán theo 4 lenses

| # | Công ty | Lens | Mô tả bài toán |
|---|---|---|---|
| 1 | Vinhomes | Lặp lại | Nhân viên CSKH Ban Quản lý phải đọc từng phản ánh dạng văn bản tự do trên app Vinhomes Resident, tự phân loại, tra cứu tòa/căn rồi chuyển tay cho đúng bộ phận. Khoảng 400 ticket/ngày, mỗi ticket mất khoảng 12 phút thao tác. |
| 2 | Vinmec | Tốn thời gian | Bác sĩ mất 20-30 phút cho mỗi bệnh nhân để viết tóm tắt hồ sơ xuất viện từ bệnh án điện tử, kết quả xét nghiệm và ghi chú điều trị. |
| 3 | VinFast | AI có thể tốt hơn | Khách hàng mô tả lỗi xe bằng tiếng Việt đời thường qua hotline, tổng đài viên không có chuyên môn kỹ thuật nên ghi nhận sai hoặc thiếu, xưởng phải gọi lại hỏi thêm, đặt lịch sửa chậm 1-2 ngày. |
| 4 | Xanh SM | Pain từ người khác | Tài xế phàn nàn điểm đón gợi ý sai (khách đứng trong ngõ nhưng pin ở mặt đường). Tài xế phải gọi xác nhận, mất 2-4 phút mỗi cuốc, tăng tỉ lệ hủy chuyến giờ cao điểm. |
| 5 | Vinpearl | AI có thể tốt hơn | Quản lý khách sạn đọc thủ công hàng trăm review mỗi ngày trên Booking, Agoda, Google Maps để tìm phàn nàn khẩn cấp. Phản hồi review rập khuôn, chậm 1-3 ngày. |
| 6 | VinFast | Lặp lại | Kế toán đối chiếu hóa đơn sạc điện từ hàng nghìn trụ sạc đối tác hằng tuần với dữ liệu hệ thống, mất 1-2 ngày công mỗi tuần. |

## Phase 2 - Quick Assess: 3 Quick Problem Cards

Top 3 được chọn: bài toán 1 (Vinhomes), 2 (Vinmec), 3 (VinFast).

### Card 1 - Vinhomes: Phân loại và điều hướng phản ánh cư dân

| Mục | Nội dung |
|---|---|
| Bài toán | Phản ánh của cư dân trên app Vinhomes Resident được đọc, phân loại và chuyển bộ phận hoàn toàn thủ công, khiến sự cố đơn giản mất nhiều giờ mới tới tay đội kỹ thuật. |
| Công ty | Vinhomes |
| Ai đang đau | Nhân viên CSKH Ban Quản lý (quá tải giờ cao điểm 18-22h); cư dân (chờ lâu, không biết ticket đi đến đâu); đội kỹ thuật (nhận ticket thiếu thông tin tòa/căn). |
| Workflow hiện tại | 1. Cư dân gửi phản ánh (text + ảnh) trên app. 2. CSKH đọc, đoán loại sự cố và mức khẩn. 3. Tra cứu tòa/căn, chọn bộ phận, chuyển qua Zalo hoặc email. 4. Soạn tin xác nhận cho cư dân. 5. Bộ phận tiếp nhận, xếp lịch, xử lý. |
| Bước tốn nhất | Bước 2-4, khoảng 12 phút thao tác mỗi ticket, cộng 4-12 giờ chờ chuyển giao. Khoảng 15% ticket bị chuyển sai bộ phận. |
| AI hỗ trợ ở đâu | Bước 2, 3, 4: đọc hiểu văn bản, trích xuất tòa/căn/loại/mức khẩn, gợi ý bộ phận, soạn nháp tin xác nhận. CSKH chỉ cần duyệt. |
| Metric thành công | Thời gian từ lúc gửi tới lúc đúng bộ phận nhận: 4-12 giờ giảm còn dưới 15 phút. Tỉ lệ phân loại đúng bộ phận từ 90% trở lên. Thời gian thao tác của CSKH: 12 phút giảm còn dưới 2 phút. |
| Kiến trúc | LLM Feature |

### Card 2 - Vinmec: Soạn tóm tắt hồ sơ xuất viện

| Mục | Nội dung |
|---|---|
| Bài toán | Bác sĩ mất 20-30 phút mỗi bệnh nhân để viết tay tóm tắt xuất viện từ bệnh án điện tử. |
| Công ty | Vinmec |
| Ai đang đau | Bác sĩ điều trị (viết tóm tắt sau giờ làm); bệnh nhân (chờ giấy xuất viện, tóm tắt khó hiểu vì thuật ngữ). |
| Workflow hiện tại | 1. Mở bệnh án điện tử đọc lại diễn biến điều trị. 2. Tổng hợp chẩn đoán, thuốc, xét nghiệm chính. 3. Viết tóm tắt và dặn dò bằng ngôn ngữ dễ hiểu. 4. Điều dưỡng kiểm tra, in, giao bệnh nhân. |
| Bước tốn nhất | Bước 1-3, 20-30 phút mỗi bệnh nhân. |
| AI hỗ trợ ở đâu | Bước 2-3: soạn nháp tóm tắt từ bệnh án, bác sĩ đọc, sửa và ký duyệt. Bắt buộc có người duyệt 100%. |
| Metric thành công | Thời gian bác sĩ dành cho tóm tắt: 25 phút giảm còn dưới 8 phút. Không có sai sót về thuốc hoặc liều trong bản đã duyệt. |
| Kiến trúc | LLM Feature |

### Card 3 - VinFast: Chẩn đoán sơ bộ lỗi xe từ mô tả của khách

| Mục | Nội dung |
|---|---|
| Bài toán | Khách mô tả lỗi xe bằng tiếng Việt đời thường, tổng đài không có chuyên môn nên ghi nhận sai hoặc thiếu, xưởng phải gọi lại. |
| Công ty | VinFast |
| Ai đang đau | Tổng đài viên (không hiểu thuật ngữ kỹ thuật); cố vấn dịch vụ tại xưởng (ticket thiếu thông tin); khách hàng (đặt lịch chậm, phải kể lại nhiều lần). |
| Workflow hiện tại | 1. Khách gọi hotline mô tả hiện tượng. 2. Tổng đài ghi tay vào CRM theo cách hiểu của mình. 3. Xưởng đọc ticket, gọi lại hỏi thêm. 4. Xếp lịch và chuẩn bị phụ tùng. |
| Bước tốn nhất | Bước 2-3, 10-15 phút cộng 1-2 ngày chờ. |
| AI hỗ trợ ở đâu | Bước 2: chuẩn hóa mô tả, gợi ý nhóm lỗi và câu hỏi làm rõ để tổng đài hỏi khách ngay trong cuộc gọi. |
| Metric thành công | Tỉ lệ ticket xưởng phải gọi lại khách: 40% giảm còn dưới 10%. Thời gian từ cuộc gọi tới lịch hẹn xác nhận: 1-2 ngày giảm còn dưới 2 giờ. |
| Kiến trúc | LLM Feature |

## Lựa chọn cho Deep-Dive: Card 1 - Vinhomes

| Card | Giá trị | Rủi ro khi AI sai | Dữ liệu sẵn có | Kết luận |
|---|---|---|---|---|
| 1 Vinhomes | Cao, lặp lại hàng trăm lần mỗi ngày | Thấp: chuyển sai thì CSKH sửa lại một click, không có hậu quả y tế hay an toàn | Lịch sử ticket đã có nhãn bộ phận xử lý | Chọn |
| 2 Vinmec | Rất cao | Rất cao: sai thuốc hoặc liều là rủi ro y tế và pháp lý | Dữ liệu nhạy cảm, khó lấy mẫu | Chưa làm |
| 3 VinFast | Cao | Trung bình | Cần bộ mã lỗi kỹ thuật nội bộ, nhóm không có | Cần thêm chuyên gia |

Kết luận: Card 1 có tỉ lệ giá trị trên rủi ro tốt nhất. Một LLM feature hẹp (phân loại, trích xuất, soạn nháp phản hồi) với con người duyệt là đủ tạo giá trị, không cần Agent.

Chi tiết Deep-Dive tại file 02-deep-dive-report.md.
