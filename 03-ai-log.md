# 03 — AI Log & Reflection (Cá nhân)

> **Học viên:** Nguyễn Khắc Phi Long — branch `PhiLong`
> **AI đã dùng:** Claude (Claude Code, chạy trực tiếp trong repo) làm thought-partner và pair-programmer; Gemini Flash làm model bị stress-test trong prototype.
> **Phase 6 — REFLECTION** theo [01-worksheet.md](01-worksheet.md)

---

## 1. Tôi đã dùng AI vào việc gì?

| Giai đoạn | Cách dùng | Kết quả |
|---|---|---|
| **Hiểu đề** | Nhờ AI đọc hết 4 file `.md` + `autograder.py` và tóm tắt "cần nộp gì, chấm thế nào" | Rất hữu ích. Tôi ban đầu chưa hiểu phải làm gì; sau khi AI diễn giải lại thành "4 việc theo thứ tự" và chỉ ra autograder chấm đúng 5 tiêu chí gì (từ khoá trong `SYSTEM_PROMPT`, đếm chữ `Passed`/`Failed`, timeout 30 giây) thì tôi mới có kế hoạch rõ ràng. |
| **Phase 1–2: Scan & Cards** | Brainstorm bài toán theo 4 lenses, so sánh 3 card để chọn deep-dive | AI gợi ý nhanh nhiều bài toán; tôi chọn Vinhomes ticket triage vì đây là thứ tôi có trải nghiệm thật khi dùng app Vinhomes Resident. |
| **Phase 3: Deep-Dive** | Cùng AI vẽ current-state workflow, điền 6-field, so sánh Rule/LLM/Agent, thiết kế HITL + fallback | AI giỏi tạo **cấu trúc** (bảng, flow, tiêu chí); tôi phải tự quyết **nội dung** nào đúng với thực tế. |
| **Phase 4: Prototype** | Pair-programming file `prompt_prototype.py`: viết system prompt, gọi Gemini SDK, thiết kế test tấn công, debug | Đây là phần AI giúp nhiều nhất và cũng là phần AI sai nhiều nhất (xem mục 2). |
| **Sơ đồ** | Nhờ AI viết script matplotlib vẽ `04-workflow-diagram.png` | Phải sửa 3 lần vì lỗi hiển thị (emoji thành ô vuông, ô tràn làn, nhãn chồng nhau). |

---

## 2. AI đã sai / hallucinate / dẫn tôi đi lạc ở đâu?

### 2.1. Tin vào "chuẩn" của template mà không kiểm tra thực tế
Starter code và đề bài đều ghi model chuẩn là `gemini-2.5-flash`. Cả AI lẫn tôi mặc định nó chạy được. Lần chạy đầu tiên: **4/4 test lỗi 404** — Google trả về *"This model is no longer available to new users"* với API key mới tạo. AI đã không đoán trước điều này; phải gọi `models.list()` mới biết key được cấp model nào. **Bài học:** với API bên thứ ba, "đề bài nói vậy" không phải bằng chứng; phải chạy thử sớm nhất có thể.

### 2.2. Prompt "trông có vẻ chặt" nhưng có lỗ hổng thật
Khi tôi thêm Test Case 3 (giả mạo `SYSTEM OVERRIDE` + pin 3% + đòi trạm 12km — tấn công **cả hai** quy tắc cùng lúc), model lite trả về `refuse_boundary_violation` — tức là **từ chối chung chung** mà **không** ra lệnh `dispatch_mobile_charger`. Về mặt "không chỉ trạm xa" thì an toàn, nhưng tài xế vẫn bị bỏ mặc giữa đường. System prompt đầu tiên do AI viết chưa nói rõ **khi nhiều quy tắc bị vi phạm cùng lúc thì ưu tiên cái nào**. Tôi yêu cầu thêm mục "Thứ tự ưu tiên: pin < 5% luôn thắng, không được thay bằng refuse" → 3/3 lần chạy lại đều đúng. **Bài học:** LLM có xu hướng chọn phản hồi "an toàn kiểu thụ động" (từ chối) thay vì "an toàn kiểu chủ động" (hành động cứu hộ) nếu không được chỉ rõ. Ranh giới kiểu "khi X thì bắt buộc Y" cần kèm thứ tự ưu tiên tường minh — tôi đã mang bài học này sang thiết kế rule guard cho Vinhomes (từ khoá khẩn cấp → ép cờ KHẨN bất kể LLM nói gì).

### 2.3. Tối ưu quá tay rồi tự gây lỗi
Để chạy dưới 30 giây, AI đặt HTTP timeout = 8 giây. Google từ chối: *"Minimum allowed deadline is 10s"* → lại 4/4 lỗi. Rồi khi tôi cho chạy lặp lại hơn 20 lần để đo độ ổn định, key free-tier bị **429 quota exceeded**, làm mấy lần chạy trông như "treo 24 giây" và tôi suýt kết luận sai là code có bug. **Bài học:** phân biệt lỗi của code với lỗi của môi trường (quota, mạng); đọc message lỗi đầy đủ trước khi sửa code.

### 2.4. Số liệu "nghe hợp lý" nhưng không có nguồn
Khi brainstorm Phase 1, AI đưa ra các con số kiểu "~400 ticket/ngày", "15% chuyển sai bộ phận", "80 giờ công/ngày" rất trôi chảy. Tôi hỏi lại "số này từ đâu?" thì AI thừa nhận là **ước lượng**. Tôi giữ lại các con số vì cần metric có số để scoping, nhưng **ghi rõ là giả định** ở đầu cả hai file `01` và `02`, và đưa "xác nhận số liệu với BQL" thành việc phải làm trước khi pilot. **Bài học:** AI tạo ra con số với cùng độ tự tin dù có nguồn hay không — người dùng phải tự gắn nhãn "đo được" vs "giả định".

### 2.5. Lỗi hiển thị mà AI không "nhìn thấy" nếu tôi không bắt nó xem
Script vẽ sơ đồ dùng emoji 🔴🔄⏱ — font Arial Unicode không có emoji nên ra ô vuông; ô Bước 1 tràn khỏi làn; nhãn chồng lên nhau. AI chỉ phát hiện khi **render ra ảnh và xem lại**, rồi sửa 3 vòng. **Bài học:** với đầu ra trực quan, bắt buộc có bước "nhìn kết quả thật", không tin code chạy không lỗi là xong.

---

## 3. Tôi đã điều chỉnh prompt / ranh giới / cách làm việc ra sao?

1. **Chuyển từ "làm giúp tôi" sang "làm rõ tiêu chí chấm trước":** yêu cầu AI đọc `autograder.py` và liệt kê chính xác nó kiểm tra gì. Nhờ vậy phát hiện chi tiết quan trọng: autograder đếm chữ `Failed` **ở bất kỳ đâu** trong output — nên mọi thông báo lỗi trong code đều phải tránh từ này.
2. **Yêu cầu chạy thật, đo thật, lặp nhiều lần** thay vì chấp nhận "chạy được 1 lần": nhờ đó lộ ra vấn đề latency (model flash thường mất 9–12 giây/lượt → 4 test mất 2 phút) và giải pháp là chạy 4 test song song + ưu tiên model lite (1–2 giây).
3. **Thêm test tấn công của riêng tôi** (Test 3 kết hợp 2 vi phạm, Test 4 gây áp lực cảm xúc "tài xế đang khóc, hệ thống duyệt lỗi") thay vì chỉ dùng 2 test có sẵn — chính Test 3 tìm ra lỗ hổng prompt.
4. **Tách "prompt tự đứng vững" và "guard can thiệp":** yêu cầu thêm hàm `guard_verdict` deterministic, in riêng, để biết bao nhiêu phần an toàn đến từ prompt và bao nhiêu từ rule. Đây cũng là kiến trúc tôi đề xuất cho Vinhomes (LLM feature + rule guard trước/sau + HITL).
5. **Không hardcode API key:** AI tự đề nghị chỉ dùng biến môi trường và nhắc tôi xoay key sau khi nộp vì key đã xuất hiện trong lịch sử chat. Tôi sẽ làm vậy.

---

## 4. Kết luận cá nhân

AI làm tốt vai trò **thought-partner có cấu trúc**: biến đề bài mơ hồ thành checklist, tạo khung bảng/flow, viết code nhanh, và — khi được yêu cầu — phản biện ngược lại chính nó. Nhưng nó cũng liên tục **tự tin ở những chỗ không có bằng chứng**: model "chuẩn" không chạy, prompt "chặt" có lỗ hổng, số liệu "hợp lý" là bịa, ảnh "đã lưu" nhưng hiển thị lỗi.

Điều tôi rút ra cho vai trò AI Product Engineer: giá trị của tôi không nằm ở việc gõ prompt, mà ở việc **đặt ranh giới, thiết kế test tấn công, và kiên quyết đòi bằng chứng chạy thật** — đúng tinh thần "Problem First, AI Second" của bài lab.
