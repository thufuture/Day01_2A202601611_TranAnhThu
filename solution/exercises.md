# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng placeholder câu trả lời mẫu bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Ở temperature 0.0–1.0, câu mở đầu gần như giống hệt nhau ("Chắc chắn rồi! Đây là một sự thật..."), cho thấy model chọn từ tiếp theo có xác suất cao nhất một cách ổn định, ít thay đổi. Ở temperature 1.5, câu mở đầu đổi hẳn sang cách diễn đạt khác ("Tuyệt vời! Dưới đây là một sự...") — cho thấy model bắt đầu lấy mẫu từ những từ có xác suất thấp hơn, tạo ra sự đa dạng/khác biệt rõ rệt so với các mức thấp hơn. Quy luật chung: temperature càng cao, output càng ít lặp lại và càng khó đoán trước.

### Câu 1.2 — Chọn temperature cho sản phẩm

**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**

> Tôi sẽ đặt temperature khoảng 0.2–0.3 (thấp). Chatbot hỗ trợ khách hàng cần trả lời nhất quán và đáng tin cậy — cùng một câu hỏi (ví dụ "chính sách đổi trả là gì?") phải luôn nhận được thông tin giống nhau qua nhiều lần hỏi, không được tự bịa thêm chi tiết không có thật. Temperature cao dễ khiến model tạo ra thông tin sai lệch (hallucination) hoặc cam kết không đúng, gây hậu quả thực tế (khiếu nại, mất uy tín). Temperature thấp giúp model bám sát dữ kiện đã biết, đổi lại câu trả lời có thể hơi cứng — đó là đánh đổi chấp nhận được cho use case này.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Theo bảng giá `PRICING_PER_1K_TOKENS`, giá output GPT-4o (0.010 USD/1K) so với GPT-4o-mini (0.0006 USD/1K) → **đắt hơn khoảng 16.7 lần**. Với workload: 10.000 người × 3 lần/ngày = 30.000 lượt gọi/ngày, mỗi lượt ~350 token output → tổng 10.500.000 token output/ngày. Chi phí GPT-4o = (10.500.000/1000) × 0.010 = **105.00 USD/ngày**; chi phí mini = (10.500.000/1000) × 0.0006 = **6.30 USD/ngày** — chênh lệch **98.70 USD/ngày** (~36.000 USD/năm) nếu dùng GPT-4o cho toàn bộ workload thay vì mini.
>
> **Nên dùng GPT-4o** cho các tác vụ cần suy luận phức tạp, độ chính xác cao, sai sót gây hậu quả lớn — ví dụ: tư vấn hợp đồng/pháp lý, debug code phức tạp, phân tích dữ liệu nhiều bước.
>
> **Nên dùng mini** cho tác vụ đơn giản, khối lượng lớn, sai sót ít nghiêm trọng — ví dụ: phân loại email/ticket hỗ trợ, trả lời FAQ, tóm tắt đoạn văn ngắn. Với quy mô 30.000 lượt/ngày như kịch bản này, chênh lệch chi phí rất lớn nên phần lớn traffic nên định tuyến qua mini, chỉ escalate lên GPT-4o khi thật sự cần.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Phản hồi "giáo viên tiểu học" ngắn hơn nhiều (~2.800 ký tự), dùng ẩn dụ đơn giản ("quyển sổ đặc biệt", "dây chuyền") và văn phong trò chuyện thân mật ("Các con tưởng tượng thế này..."), hoàn toàn không có thuật ngữ kỹ thuật. Phản hồi "chuyên gia tài chính" dài hơn hẳn (~5.450 ký tự, gấp ~1.9 lần), sử dụng dày đặc thuật ngữ chuyên ngành (distributed ledger, mã băm, Proof-of-Work, Proof-of-Stake, PBFT...) và cấu trúc phân mục kỹ thuật rõ ràng. Cùng một câu hỏi, system prompt đã thay đổi hoàn toàn độ dài, mức độ chuyên sâu và từ vựng của câu trả lời — chứng minh persona không chỉ đổi "giọng điệu" mà đổi cả nội dung thực chất model chọn để trình bày.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Với đoạn văn tiếng Việt 120 từ (chủ đề blockchain), `count_tokens(text, model="gpt-4o")` cho ra **148 token thật**, trong khi ước lượng thô `120 / 0.75 = 160 token` — chênh **-7.5%** so với ước lượng (ước lượng cao hơn thực tế trong trường hợp này). Tỷ lệ thực tế là **1.23 token/từ**. Để đối chiếu, dịch cùng nội dung sang tiếng Anh (79 từ) chỉ tốn 92 token — tỷ lệ **1.16 token/từ**, thấp hơn tiếng Việt. Nguyên nhân: bộ mã hóa BPE của tiktoken được huấn luyện chủ yếu trên dữ liệu tiếng Anh, nên các từ tiếng Anh phổ biến thường gộp trọn thành 1 token; còn ký tự có dấu thanh điệu tiếng Việt (ă, â, ê, ô, ơ, ư và các dấu sắc/huyền/hỏi/ngã/nặng) nằm ngoài bảng ký tự cơ bản, thường bị tách thành nhiều token con (byte-level) hơn cho cùng một từ.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất trong các ứng dụng **tương tác trực tiếp với người dùng** như chatbot, trợ lý ảo — nơi câu trả lời có thể dài và độ trễ cảm nhận (perceived latency) ảnh hưởng trực tiếp đến trải nghiệm: thay vì người dùng nhìn màn hình trống vài giây rồi cả đoạn văn xuất hiện cùng lúc, streaming cho họ thấy chữ xuất hiện ngay lập tức và có thể bắt đầu đọc trong khi model vẫn đang sinh phần sau, giúp ứng dụng "cảm giác nhanh" hơn dù tổng thời gian xử lý không đổi. Ngược lại, non-streaming phù hợp hơn khi ứng dụng cần **xử lý toàn bộ response trước khi dùng được** — ví dụ: parse JSON có cấu trúc, chạy tiếp một bước xử lý khác trên kết quả đầy đủ (như `estimate_cost` cần cả câu trả lời để đếm token), gọi API trong tiến trình nền không có người xem trực tiếp, hoặc khi cần đảm bảo tính toàn vẹn của response trước khi hiển thị (tránh hiện nội dung dở dang rồi phải sửa).

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff (delay tăng gấp đôi mỗi lần: 0.1s → 0.2s → 0.4s...) giúp giãn các lần retry ra xa nhau theo thời gian, cho server đang quá tải có cơ hội hồi phục dần thay vì bị đánh dồn dập liên tục ở cùng một tần suất. Nếu dùng delay cố định (ví dụ luôn 1 giây) và **hàng nghìn client cùng gặp lỗi tại thời điểm gần nhau** (ví dụ server vừa restart hoặc vừa hết quá tải tạm thời), tất cả client sẽ đồng loạt retry lại đúng sau 1 giây — tạo ra một đợt tải đột biến mới (gọi là "thundering herd" — hiệu ứng bầy đàn), có thể làm server vừa hồi phục lại sập tiếp ngay lập tức, tạo thành vòng lặp lỗi liên tục. Exponential backoff (thường kết hợp thêm "jitter" — độ trễ ngẫu nhiên nhỏ) làm các lần retry của các client rải đều ra theo thời gian thay vì dồn cùng một lúc, giảm đáng kể nguy cơ này.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Tôi chọn persona "trợ giảng AI" hỗ trợ sinh viên ôn tập:
>
> *"Bạn là trợ giảng AI thân thiện của khóa học AI Practical Competency. Luôn trả lời bằng tiếng Việt, ngắn gọn trong 3-4 câu trừ khi người dùng yêu cầu giải thích chi tiết hơn. Nếu không chắc chắn về một thông tin, hãy nói rõ là không chắc thay vì bịa đặt."*
>
> Hai lựa chọn từ ngữ quan trọng: (1) **"Luôn trả lời bằng tiếng Việt"** — chỉ định ngôn ngữ tường minh vì model có thể tự chuyển sang tiếng Anh nếu người dùng gõ lẫn thuật ngữ Anh trong câu hỏi, cần khóa cứng để trải nghiệm nhất quán. (2) **"ngắn gọn trong 3-4 câu... hãy nói rõ là không chắc thay vì bịa đặt"** — giới hạn độ dài giúp tiết kiệm token/chi phí và tránh trợ lý lan man; yêu cầu thừa nhận không chắc chắn là kỹ thuật giảm hallucination cơ bản, quan trọng với vai trò trợ giảng vì thông tin sai có thể khiến sinh viên học nhầm kiến thức.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất: **history chỉ giữ 3 lượt gần nhất** (`history[-6:]`) — nếu cuộc trò chuyện đề cập một chi tiết quan trọng ở lượt thứ 1 (ví dụ sinh viên nói tên môn học đang ôn), rồi hỏi tiếp 4-5 lượt khác, trợ lý sẽ "quên" hoàn toàn chi tiết đó vì nó đã bị cắt khỏi history, dẫn đến trả lời thiếu ngữ cảnh hoặc phải hỏi lại thông tin đã cung cấp.
>
> **Cải thiện đề xuất:** thêm một bước **tóm tắt (summarization)** trước khi cắt history — thay vì xóa thẳng các lượt cũ, gọi thêm 1 lần API (hoặc dùng mini model cho rẻ) để tóm tắt 3 lượt sắp bị cắt thành 1-2 câu ngắn, rồi chèn câu tóm tắt đó vào đầu history dưới dạng 1 message hệ thống bổ sung (ví dụ `{"role": "system", "content": "Tóm tắt hội thoại trước: ..."}`). Cách này giữ được ngữ cảnh dài hạn quan trọng mà không để token input phình to không kiểm soát như khi giữ nguyên toàn bộ lịch sử.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
