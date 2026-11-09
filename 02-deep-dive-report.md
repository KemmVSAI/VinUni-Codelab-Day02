🏗️ Phase 3 — DEEP-DIVE (Nhóm)
3.1. Current-State Workflow
Quy trình xử lý sự cố hết pin thực địa hiện tại của điều phối viên Xanh SM:
Text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận báo cáo │     │ Tra cứu định │     │ Tra cứu trạm │     │ Soạn văn bản │
│ sự cố pin    │ ──→ │ vị GPS & %pin│ ──→ │ sạc VinFast  │ ──→ │ hướng dẫn    │
│              │     │              │     │ còn trụ trống│     │ gửi tài xế   │
│ Ai: Dispatch │     │ Ai: Dispatch │     │ Ai: Dispatch │     │ Ai: Dispatch │
│ ⏱ 2 phút     │     │ ⏱ 2 phút     │     │ ⏱ 5 phút 🔴  │     │ ⏱ 5 phút 🔴  │
│ In: App/Call │     │ In: Biển số  │     │ In: Vị trí GPS│     │ In: Raw data │
│ Out: Log sự cố│     │ Out: Toạ độ  │     │ Out: Địa chỉ │     │ Out: SMS/App │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ Gọi xe sạc   │
                                                               │ cứu hộ (<5%) │
                                                               │ Ai: Dispatch │
                                                               │ ⏱ 1 phút     │
                                                               └──────────────┘
🔴 = Bottlenecks (Tốn thời gian tra cứu chéo và dễ tính nhầm khoảng cách an toàn khi pin < 5%)
🔄 = Handoffs giữa Tài xế ──→ Điều phối viên ──→ Hệ thống bản đồ trạm sạc ──→ Đội xe cứu hộ pin
⏱ Tổng thời gian xử lý thủ công: 15 phút/lượt.
3.2. Problem Statement (6-field) — Vin Smart Future Standard
Field	Nội dung
1. Actor / Operator	Điều phối viên (Dispatcher) thuộc Trung tâm Điều vận Xanh SM (GSM).
2. Current Workflow	Khi tài xế báo pin yếu/cạn pin, điều phối viên tra cứu tọa độ GPS trên bản đồ nội bộ, mở Dashboard trạm sạc VinFast (V-Green) tìm trụ sạc trống gần nhất, tự ước lượng quãng đường an toàn, gõ tin nhắn chỉ dẫn gửi qua App tài xế, hoặc tạo lệnh gọi xe sạc cứu hộ lưu động nếu pin dưới 5%. Quy trình gồm 5 bước thủ công, mất trung bình 15 phút/lượt.
3. Bottleneck	Bước 3 & 4 (mất 10 phút): Tra cứu thủ công trụ sạc trống phù hợp với dòng xe (VF5/VFe34/VF8), kiểm tra điều kiện an toàn (pin < 5% không được đi quá 5km) và soạn thảo tin nhắn hướng dẫn chi tiết cho tài xế dưới áp lực thời gian thực.
4. Business Impact	Trung bình mỗi ngày phát sinh ~80–100 ca hỗ trợ pin khẩn cấp tại Hà Nội & TP.HCM, tiêu tốn ~20–25 giờ công/ngày của đội điều vận. Nếu điều phối viên sơ suất chỉ xe còn < 5% pin đi trạm xa > 5km, xe sập nguồn giữa đường sẽ tốn 1.5 – 2.5 triệu VNĐ/ca cẩu kéo và làm giảm ~15% doanh thu ca chạy của xe đó.
5. Success Metric	1. Efficiency: Giảm tổng thời gian xử lý và phản hồi sự cố từ 15 phút xuống dưới 2 phút/lượt.
2. Safety & Quality: Đạt 100% tin nhắn đầu ra có gắn nhãn [DRAFT_ONLY] chờ duyệt và 0% trường hợp xe pin < 5% bị điều hướng tới trạm sạc xa quá 5km (tự động kích hoạt xe sạc lưu động đạt chuẩn 99%).
6. Operational Boundary	AI ĐƯỢC PHÉP: Truy xuất dữ liệu vị trí xe, trạng thái trạm sạc VinFast, tự động soạn bản nháp hướng dẫn ([DRAFT_ONLY]) hoặc sinh lệnh JSON gọi xe sạc lưu động (dispatch_mobile_charger).
CẤM TUYỆT ĐỐI: AI không được tự động gửi thẳng tin nhắn cho tài xế khi chưa có phê duyệt của điều phối viên (Bắt buộc HITL qua thẻ [DRAFT_ONLY]); tuyệt đối không được đề xuất trạm sạc cách xa quá 5km khi pin hiện tại dưới 5%.
3.3. Future-State Flow & AI Fit
AI Fit: Chọn LLM Feature (kết hợp System Prompt Guardrails chặt chẽ). Không dùng Rule-based thuần túy vì tin nhắn báo sự cố của tài xế là ngôn ngữ tự nhiên phi cấu trúc, nhiều biến thể; đồng thời không dùng Agent tự trị hoàn toàn vì rủi ro điều hướng sai khi pin cạn có thể gây chết máy giữa đường, bắt buộc phải giữ con người ở khâu phê duyệt cuối (Human-in-the-loop).
Quy trình tương lai (Future-State):
Text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận báo cáo │     │ 🔵 Auto-pull │     │ 🔵 AI draft  │     │ 🟢 Dispatch  │
│ sự cố từ TX  │ ──→ │ GPS, % pin & │ ──→ │ `[DRAFT_ONLY]`│ ──→ │ click duyệt  │
│              │     │ trạm sạc trống│    │ hoặc lệnh    │     │ & gửi tài xế │
│              │     │              │     │ cứu hộ (<5%) │     │ / đội cứu hộ │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ↩️ Fallback:
                                                               Nếu AI trả về lỗi
                                                               hoặc thiếu thẻ
                                                               `[DRAFT_ONLY]`, hệ
                                                               thống khóa nút gửi
                                                               và để Dispatcher
                                                               tự xử lý tay như cũ.
🏁 Phase 5 — EVALUATE & Kết luận từ buổi Lab
AI Readiness Checklist:

Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? (Đã có dữ liệu GPS đội xe Xanh SM, API trạng thái trụ sạc V-Green và lịch sử log điều vận).

Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? (Hoàn toàn nằm trong tầm kiểm soát nhờ chốt chặn [DRAFT_ONLY] bắt buộc điều phối viên duyệt trước khi gửi và cơ chế tự động gọi cứu hộ khi pin < 5%).

Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? (Điều phối viên Xanh SM rất sẵn sàng vì công cụ giúp giảm thời gian thao tác thủ công từ 15 phút xuống dưới 2 phút/ca trong giờ cao điểm).
Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[x] GO (Bắt đầu xây dựng Prototype với scope hẹp tại Trung tâm Điều vận Xanh SM Hà Nội).
Lý giải (Justification): Dự án giải quyết trực tiếp nỗi đau vận hành thời gian thực của GSM, có chỉ số đo lường (ROI & SLA) rõ ràng, kiến trúc LLM Feature trên nền Gemini 2.5 Flash có chi phí cực thấp, độ trễ nhanh (~1.5 giây) và đã chứng minh khả năng bảo vệ ranh giới an toàn tuyệt đối qua các bài kiểm thử tấn công (Adversarial Stress-Tests).
