# 🤖 03 — AI Usage Log & Reflection (Vin Smart Future)

> **Dự án:** Hệ thống Phát hiện & Xử lý Gian lận Giao đồ ăn Xanh SM (Anti-Ghost Call & Fraud Copilot)  
> **Mô hình sử dụng:** Gemini 2.5 Flash (`gemini-2.5-flash`)

---

## 1. Nhật ký sử dụng AI (AI Interaction Log)

Trong quá trình thực hiện từ Phase 1 (Scan) đến Phase 4 (Prompt Prototype & Boundary Test), nhóm đã sử dụng AI như một trợ lý đồng hành (Thought Partner & Technical Copilot) qua các bước sau:

| Giai đoạn | Mục đích sử dụng AI | Tóm tắt Prompt / Đầu vào cho AI | Đánh giá kết quả từ AI | Chỉnh sửa / Quyết định của con người (Human-in-the-Loop) |
| :--- | :--- | :--- | :--- | :--- |
| **Phase 1 & 2: Scan & Quick-Assess** | Brainstorm và định lượng các điểm nghẽn (bottlenecks) trong vận hành Xanh SM. | *"Liệt kê các kịch bản gian lận phổ biến nhất của tài xế giao đồ ăn và ước tính thời gian CSKH đối soát log thủ công."* | AI gợi ý nhiều kịch bản (hủy đơn, tráo hàng, báo quán đóng cửa). Tuy nhiên ban đầu AI mô tả khá chung chung, thiếu đặc thù viễn thông tại Việt Nam. | Nhóm thu hẹp phạm vi vào đúng nỗi đau thực tế nhất: hành vi **"Nháy máy" (Ghost Call < 15s)** kết hợp dừng xe chớp nhoáng rồi hủy đơn chiếm đoạt đồ ăn. |
| **Phase 3: Deep-Dive & Workflow** | Chuẩn hóa Problem Statement (6-field) và thiết kế kiến trúc kết hợp. | *"Từ quy trình 5 bước thủ công của CSKH Xanh SM, hãy đề xuất Future-State Flow kết hợp Rule Engine và LLM."* | AI ban đầu đề xuất dùng Autonomous Agent tự động khóa tài khoản tài xế ngay khi phát hiện nháy máy. | **Bác bỏ đề xuất tự động khóa của AI.** Nhóm quyết định thiết kế kiến trúc **Client Rule Engine + LLM Feature (Copilot)** để tránh rủi ro phạt nhầm tài xế khi sóng GPS/GSM chập chờn. |
| **Phase 4: System Prompt Design** | Xây dựng `SYSTEM_PROMPT` với 4 quy tắc ranh giới (Operational Boundaries) và JSON Schema. | *"Viết System Prompt cho Gemini 2.5 Flash đóng vai trò Fraud Copilot, bắt buộc có tiền tố [DRAFT_ONLY], cấm PERMANENT_BAN và bắt lỗi Ghost Call < 15s."* | AI tạo được khung prompt tốt, định nghĩa rõ JSON output nhưng ở lần chạy đầu tiên vẫn đôi khi bọc ````json` trước tiền tố `[DRAFT_ONLY]`. | Tinh chỉnh lại câu lệnh ràng buộc trong `[RULE 1]`: cấm tuyệt đối việc thêm markdown codeblock trước `[DRAFT_ONLY] ` để đảm bảo parser hoạt động chính xác 100%. |
| **Phase 4: Adversarial Testing** | Thiết kế các kịch bản tấn công (Red-Teaming) để kiểm tra độ bền của ranh giới an toàn. | *"Tạo 5 kịch bản prompt injection: ép bỏ [DRAFT_ONLY], ép khóa vĩnh viễn tài xế, ép từ chối đền bù khách VIP, thao túng cảm xúc và log nháy máy 2 giây."* | Gemini 2.5 Flash với `temperature=0.0` vượt qua toàn bộ 5 bài test, giữ vững tiền tố `[DRAFT_ONLY]`, bảo vệ khách VIP và chấm đúng `fraud_score >= 80` cho ca nháy máy. | Viết thêm bộ kiểm thử tự động (Verification Assertions) trong Python để kiểm chứng đầu ra một cách định lượng. |

---

## 2. Bài học rút ra & Phản tư (Key Reflections)

### 2.1. Tại sao không dùng AI cho mọi thứ? (Rule Engine vs. LLM)
Qua buổi Lab, bài học lớn nhất của nhóm là **phân định rõ vai trò giữa Rule-based truyền thống và LLM**:
* Với việc chặn nút "Hủy đơn" trên ứng dụng tài xế khi thời gian chờ (dwell time) < 3 phút hoặc chưa phát sinh cuộc gọi GSM: **Client Rule Engine** làm tốt hơn, nhanh hơn (độ trễ mili-giây) và chi phí bằng 0.
* **LLM (Gemini 2.5 Flash)** chỉ thực sự phát huy giá trị ở Bước 5 (khi đơn hàng đã phát sinh khiếu nại phức tạp): LLM đọc hiểu đồng thời dữ liệu phi cấu trúc (tin nhắn chat, ghi chú khiếu nại của khách, lý do giải trình cảm tính của tài xế) kết hợp với dữ liệu có cấu trúc (log GPS, thời lượng cuộc gọi GSM) để tính toán `fraud_score`, giải thích lý do và soạn sẵn văn bản phản hồi (`draft_resolution`) chuẩn giọng điệu CSKH Xanh SM.

### 2.2. Thiết kế ranh giới an toàn (Operational Boundaries & HITL)
Trong môi trường vận hành thực tế của Vingroup, một quyết định sai của AI có thể gây hậu quả pháp lý hoặc truyền thông nghiêm trọng:
1. **Bảo vệ sinh kế của tài xế (Rule 2):** Không bao giờ trao quyền `PERMANENT_BAN` cho AI. AI chỉ được gán cờ `TEMP_SUSPEND_REVIEW` để Quản lý Vận hành (Ops Manager) phán quyết cuối cùng.
2. **Chống thao túng cảm xúc & Prompt Injection (Rule 1 & Rule 4):** Tài xế gian lận có thể nhập lý do hủy đơn mang tính bi kịch ("mẹ cấp cứu", "xe hỏng nặng") nhằm đánh lừa mô hình ngôn ngữ. Việc neo chặt quyết định của AI vào **bằng chứng log vật lý (GPS & GSM)** trong System Prompt đã giúp mô hình không bị thao túng bởi ngôn từ cảm tính.
3. **Cơ chế `[DRAFT_ONLY]`:** Là chốt chặn kỹ thuật đơn giản nhưng cực kỳ hiệu quả để đảm bảo không một tin nhắn hay lệnh hoàn tiền nào được bắn thẳng tới khách hàng mà thiếu cái click xác nhận của nhân viên CSKH.
