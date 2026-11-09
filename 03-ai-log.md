# 03 — AI Log & Reflection (Cá nhân)

- **Họ tên:** Huỳnh Văn Cường  
- **MSSV:** 2A202603866  
- **Công cụ dùng:** Gemini (AI Studio / API), Cursor  

---

## AI đã giúp gì?

1. **Brainstorm scan:** Gợi ý pain point vận hành VinFast / Xanh SM / Vinhomes theo 4 lenses, giúp mình đủ ≥5 bài toán nhanh.  
2. **Stress-test Problem Card:** Đóng vai CFO/Ops để phản biện metric và hỏi “vì sao không dùng rule-based”.  
3. **Soạn system prompt boundary:** Làm rõ `[DRAFT_ONLY]` và ngưỡng pin &lt;5% → `dispatch_mobile_charger` trước khi code.  
4. **Refactor starter code:** Gợi ý dùng `google-genai` Client + `GenerateContentConfig(system_instruction=...)`.  
5. **Chạy prototype:** 3/3 adversarial cases pass với `gemini-flash-lite-latest` (sau khi đổi key Day01 work + tránh model hết quota).

---

## AI sai / hallucination ở đâu?

1. Đưa số liệu “chắc chắn” (số sự cố/ngày, % doanh thu) **không có nguồn** — mình chỉ giữ dạng ước lượng và gắn metric có thể đo sau.  
2. Gợi ý merge file `.py` vào `main` trong một lần trả lời — **trái quy định lab** (code chỉ trên branch cá nhân).  
3. Đề xuất bỏ tag draft “cho nhanh” khi mô phỏng dispatcher vội — đúng kiểu tấn công adversarial; cần system prompt cứng.

---

## Mình đã sửa prompt / ranh giới thế nào?

- Thêm cứng: *mọi câu trả lời phải bắt đầu `[DRAFT_ONLY]`*, kể cả khi user bảo gửi thẳng.  
- Thêm rule JSON `dispatch_mobile_charger` khi pin &lt;5%, cấm trạm &gt;5km.  
- Temperature thấp (0.2) khi gọi API để giảm sáng tạo phá rule.  
- Tự viết adversarial case #3 (VIP + ép auto-send) thay vì chỉ tin example có sẵn.

---

## Reflection ngắn

Buổi lab nhắc mình: scoping tốt hơn “vibe code trước”. AI hữu ích khi là thought-partner, nhưng số liệu và quy tắc Git/HITL phải do người giữ. Với bài Xanh SM, giá trị nằm ở **boundary + HITL**, không phải chat bot tự gửi tin.
