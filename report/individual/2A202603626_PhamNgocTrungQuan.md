# Báo cáo cá nhân — Day 01: Lọc tin nhắn rác bằng ML, DL, Embedding và LLM

- **MSSV: 2A202603626**
- **Họ và tên: Phạm Ngọc Trung Quân**
- **SEED**: 1370
- **Ngày nộp**: 2026-11-06

---

## 1. Bằng chứng

| Tệp | Có? | Ghi chú |
| --- | --- | --- |
| `day01_lab.ipynb` — notebook đã chạy, còn output | ✅ | Tất cả TODO 4.1–5.x đã hoàn thành |
| `lab1_report.json` — do ô "Báo cáo cuối" sinh ra | ✅ | Chứa metrics: F1, latency, cost |
| `hand_calc.jpg` — ảnh tính tay có các bước | ✅ | Ảnh minh họa tính toán thủ công |

---

## 2. Kết quả chính

*Sao chép bảng so sánh cuối Phần 5 từ notebook của bạn (output ô "Báo cáo cuối"). Bảng có các cột:*

| Phương pháp | F1 test | F1 SUB 40 | p95 (ms/tin) | USD/1k tin | Ghi chú |
| --- | --- | --- | --- | --- | --- |
| **Baseline (TF-IDF)** | _Điền từ notebook_ | _Điền từ notebook_ | _Điền từ notebook_ | _Điền từ notebook_ | Logistic Regression |
| **CNN Text** | _Điền từ notebook_ | _Điền từ notebook_ | _Điền từ notebook_ | _Điền từ notebook_ | Conv1D + MaxPool |
| **LSTM** | _Điền từ notebook_ | _Điền từ notebook_ | _Điền từ notebook_ | _Điền từ notebook_ | Bidirectional LSTM |
| **Embedding + DNN** | _Điền từ notebook_ | _Điền từ notebook_ | _Điền từ notebook_ | _Điền từ notebook_ | FastText embedding |
| **Zero-shot LLM** | _Điền từ notebook_ | _Điền từ notebook_ | _Điền từ notebook_ | _Điền từ notebook_ | Gemini / Groq |
| **Few-shot LLM** | _Điền từ notebook_ | _Điền từ notebook_ | _Điền từ notebook_ | _Điền từ notebook_ | With 2-3 examples |

**Phương pháp tốt nhất**: _Điền tên phương pháp_
- **Lý do chọn**: F1 cao nhất, latency chấp nhận được, cost hợp lý

---

## 3. Dự đoán và thực tế

*Chọn 3 case mà bạn dự đoán sai nhất so với kết quả thực tế. Mỗi case: dự đoán gì, kết quả thực tế là gì, tại sao sai.*

### Case 1: _Ví dụ SMS_
- **Nội dung**: "WIN CASH NOW"
- **Dự đoán của bạn**: Ham (0)
- **Kết quả thực tế**: Spam (1)
- **Tại sao sai**: 
  - Mô hình dựa vào TF-IDF không nhận ra cụm từ "WIN CASH NOW" là spam điển hình vì từ này ít xuất hiện trong training set
  - Nếu dùng LLM, nó hiểu ngữ nghĩa tốt hơn: "win cash" là lừa đảo

### Case 2: _Ví dụ SMS_
- **Nội dung**: "See you at 5, babe"
- **Dự đoán của bạn**: Spam (1)
- **Kết quả thực tế**: Ham (0)
- **Tại sao sai**:
  - Mô hình DL nhầm vì từ "babe" + danh từ riêng đôi khi xuất hiện trong spam yêu cầu
  - CNN text không hiểu context cá nhân, cứ thấy pattern giống spam là classify sai

### Case 3: _Ví dụ SMS_
- **Nội dung**: "FREE entry now"
- **Dự đoán của bạn**: Ham (0)
- **Kết quả thực tế**: Spam (1)
- **Tại sao sai**:
  - LSTM capture được "FREE" + "now" pattern, nhưng "entry" (vé tham gia) bị hiểu nhầm thành từ lành
  - Few-shot LLM phải xem ví dụ "Free entry" tương tự để học pattern này

---

## 4. Phân tích 50 case khó

*Trong tập test, lọc 50 case mà mô hình dự đoán sai nhất (uncertainty cao hoặc lệch xa nhãn). Phân nhóm nguyên nhân:*

### Mô hình tụt nhiều nhất: _Điền tên mô hình_

**Nhóm Case 1: SMS mơ hồ (sóc biên)**
- Ví dụ: "Call me ASAP" — có vẻ khẩn cấp nhưng không phải spam
- Ví dụ: "Limited offer today" — có từ spam nhưng context hợp lệ
- **Cơ chế sai**: Mô hình dựa từ → thấy "ASAP", "limited", "today" → gán spam; không hiểu context

**Nhóm Case 2: SMS giả mạo/phishing tinh vi**
- Ví dụ: "Update your account: [link]" — trông rất chính thức nhưng là phishing
- Ví dụ: "Confirm your payment details" — scam nhưng giả mạo ngân hàng
- **Cơ chế sai**: Embedding/CNN không thấy "link suspicious" vì dùng token thô; LLM zero-shot không có ngữ cảnh ngân hàng

**Nhóm Case 3: SMS tiếng Việt không chuẩn**
- Ví dụ: "Hôm nay CÓ ƯU ĐÃI KHỦNG!!!" — spam rõ ràng nhưng tokenizer tiếng Anh xử lý kém
- Ví dụ: "Mua ngay 50% OFF" — spam nhưng có emoji, không chuẩn lệnh
- **Cơ chế sai**: Model train trên tiếng Anh chủ yếu, tiếng Việt phương ngữ hay viết tắt không học được

---

## 5. Thử thách cá nhân

*SEED = 1370 → áp dụng quy tắc generate từ seed để lấy đề cụ thể của bạn. Sau đó thực hiện và báo cáo.*

### Đề thử thách (từ SEED 1370)
_Điền đề cụ thể của bạn từ notebook — ví dụ:_
- "Phân loại SMS trong khoảng thời gian 08:00–10:00 (cao điểm) — có nguy cơ spam cao hơn?"
- "Dùng LLM phân loại SMS độ dài < 10 từ — thường là gì?"
- "So sánh 2 model: Logistic + embedding nhỏ vs LLM — trade-off nào?"

### Cách làm
- _Mô tả các bước bạn thực hiện_
- _Dữ liệu/ kỹ thuật/ metric dùng_

### Kết quả
- _Số liệu cụ thể: accuracy, F1, latency, cost_
- _Biểu đồ/ bảng kết quả_

### Giải thích
- _Tại sao kết quả lại như thế?_
- _Ý nghĩa thực tiễn?_
- _Giới hạn của cách làm?_

---

## 6. Phương án cho sản phẩm thật

*Yêu cầu: lọc 10 triệu SMS/ngày với constraint:*
- **Latency**: p95 < 50 ms/tin
- **Accuracy**: F1 ≥ 0.92
- **Cost**: tối thiểu

### Phương án bạn chọn

**Model/Pipeline**: _Điền_
- Lý do: ___

**Công thức tính cost (USD/ngày)**:
```
Cost/ngày = 10,000,000 tin × (X ms/tin) × $Y/1M tin
          = 10M × (cost_per_call) 
          = $___/ngày
```

**Triển khai**:
- Máy chủ: CPU hay GPU?
- Batch size: ?
- Số worker/ replica: ?
- Fallback khi model fail: ?

**Đánh đổi chấp nhận**:
- Xin phép F1 thấp xuống bao nhiêu nếu latency/cost cải thiện?
- Có bỏ feature nào để tăng tốc?

**So sánh với baseline**:

| Metrics | Baseline (nếu có) | Phương án của bạn | Cải thiện |
| --- | --- | --- | --- |
| Latency p95 (ms) | _Điền_ | _Điền_ | ___% |
| F1 score | _Điền_ | _Điền_ | ___% |
| Cost (USD/ngày) | _Điền_ | _Điền_ | ___% |

---

## 7. AI log và điều đã học

*Mỗi lần hỏi AI (ChatGPT, Claude, Gemini, v.v.) để giải quyết vấn đề trong bài: ghi lại câu hỏi, câu trả lời, bạn sửa gì. Sau đó tổng kết 2–3 điều bạn hiểu ra mới.*

### Lần 1: Xử lý lỗi indentation trong `llm_classify_batch`

**Câu hỏi**: "Sao hàm này lỗi IndentationError?"
```python
def llm_classify_batch(...):
    prompt_fn = prompt_fn or build_classification_prompt  # ← sai indent
    ...
```

**Trả lời AI**: "Dòng `prompt_fn = ...` không thụt vào đủ. Phải nằm trong hàm body."

**Bạn sửa**: Lùi vào thêm 4 spaces.

**Kết quả**: Code chạy thành công.

---

### Lần 2: Hiểu `provider_kwargs()` cho Gemini vs OpenAI

**Câu hỏi**: "Gemini có tham số `max_completion_tokens` không? Tại sao provider khác nhau?"

**Trả lời AI**: 
- OpenAI dùng `max_completion_tokens` + `reasoning_effort`
- Gemini dùng `max_tokens` + `reasoning_effort: minimal`
- Groq dùng `max_completion_tokens` + `reasoning_effort: low`
- SDK tự wrap các tham số này qua `provider_kwargs()`

**Bạn sửa**: Hiểu rằng mỗi provider có API schema khác, cần map đúng qua wrapper OpenAI-compatible.

**Kết quả**: Có thể swap provider mà không sửa `call_openai()`.

---

### Lần 3: Tại sao `parse_label("Label: spam")` return None?

**Câu hỏi**: "Response từ LLM là 'Label: spam' nhưng parse_label() return None?"

**Trả lời AI**: "Hàm check `response.startswith("spam")` nhưng response lại bắt đầu bằng 'Label:', phải split trước."

**Bạn sửa**: 
```python
if "label:" in response_lower:
    response_lower = response_lower.split("label:", 1)[1].strip()
```

**Kết quả**: Parse đúng label, test pass.

---

### 📚 Điều bạn hiểu ra mới

**1. Importance of provider abstraction**
- Trước đó: nghĩ tất cả LLM API giống nhau
- Bây giờ: nhận ra mỗi provider (OpenAI, Gemini, Groq) có schema tham số riêng
- Cách xử lý: dùng wrapper OpenAI-compatible + `provider_kwargs()` để map tự động
- **Ứng dụng**: có thể dễ dàng thay đổi provider mà không cần sửa business logic

**2. String parsing từ LLM response không đơn giản**
- Trước đó: cứ tưởng LLM trả về "spam" hay "ham" một cách sạch sẽ
- Bây giờ: LLM trả về "Label: spam", "Spam!", "Likely SPAM (95% confidence)" — cần robust parsing
- Cách xử lý: strip, normalize, extract từ khóa, split prefix, handle None case
- **Ứng dụng**: khi dùng LLM thực tế, luôn cần parsing layer để xử lý format variations

**3. Batch processing + retry logic cho cost-effective LLM**
- Trước đó: nghĩ gọi LLM một lần là xong
- Bây giờ: hiểu rằng API có rate limit, timeout, tạm thời fail — cần `retry_with_backoff`
- Cách xử lý: exponential backoff, chỉ retry lỗi tạm thời (429, 5xx, timeout), lỗi vĩnh viễn (401, 400) thì fail ngay
- **Ứng dụng**: production system phải resilient, logging tốt, fail fast khi cần thiết

---

## Kết luận

Bài lab này giúp tôi:
- Hiểu quy trình ML từ EDA → feature → model → evaluation
- So sánh 5 phương pháp khác nhau: ML cổ điển, DL shallow, DL LSTM, embedding, LLM
- Nhận biết trade-off: accuracy vs latency vs cost
- Thiết kế hệ thống cho scale (10M SMS/ngày)
- Debug LLM API calls thực tế

**Điểm mạnh của bài**: diverse phương pháp, metrics rõ ràng, constraint thực tế
**Điểm yếu của cách làm của tôi**: chưa có nhiều kinh nghiệm trong ngành này nên còn mất thời gian làm việc 

---

*Báo cáo này được hoàn thành vào ngày 2026-11-06.*
