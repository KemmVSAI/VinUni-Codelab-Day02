🏗️ Phase 3 — DEEP-DIVE (Nhóm)
3.1. Current-State Workflow
Quy trình tiếp nhận và đối soát khiếu nại gian lận giao đồ ăn hiện tại của đội ngũ CSKH/Ops Xanh SM:
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Tài xế đến   │     │ Chờ 5-10p    │     │ Tài xế bấm   │     │ Khách tra cứu│
│ điểm giao &  │ ──→ │ không liên lạc│ ──→│ "Khách không │ ──→ │ thấy đơn đã  │
│ nháy máy app │     │ qua số GSM   │     │ nhận" / Hủy  │     │ giao/bị hủy  │
│ Ai: Driver   │     │ Ai: Driver   │     │ Ai: Driver   │     │ Ai: Customer │
│ ⏱ 1 phút     │     │ ⏱ 5-10 phút  │     │ ⏱ 1 phút     │     │ ⏱ 2 phút     │
│ In: Tọa độ   │     │ In: App chat │     │ In: Nút hủy  │     │ In: App Xanh │
│ Out: Ping app│     │ Out: Miss-log│     │ Out: Log hủy │     │ Out: Bức xúc │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ Khiếu nại &  │
                                                               │ đối soát log │
                                                               │ điều tra lận │
                                                               │ Ai: CSKH/Ops │
                                                               │ ⏱ 15-30p 🔴  │
                                                               │ In: Ticket   │
                                                               │ Out: Bồi hoàn│
                                                               └──────────────┘
🔴 = Bottlenecks
⏱ Tổng thời gian xử lý thủ công: 24 - 44 phút/vụ việc (thời gian khách chờ giải quyết kéo dài đến 24h).

3.2. Problem Statement (6-field) — Vin Smart Future Standard
Field,Nội dung
1. Actor / Operator,Nhân viên Chăm sóc Khách hàng (CSKH) & Chuyên viên Vận hành (Ops) phụ trách xử lý khiếu nại mảng Giao đồ ăn Xanh SM.
2. Current Workflow,"Tài xế đến điểm giao nhưng chỉ nháy máy/nhắn qua in-app chat mà không gọi trực tiếp qua GSM. Sau 5–10 phút, tài xế tự ý bấm ""Khách không nghe máy"" hoặc ""Hoàn thành"" để chiếm đoạt món ăn. Khách hàng không nhận được hàng phải gọi hotline/gửi ticket khiếu nại. CSKH tiếp nhận và phải mở nhiều màn hình kiểm tra thủ công: kiểm tra log tọa độ GPS, thời gian dừng xe (dwell time), lịch sử gọi điện qua tổng đài và lịch sử tài xế để ra quyết định bồi thường. Toàn bộ chu trình đối soát mất 15–30 phút thao tác tay cho mỗi vụ việc, kéo dài thời gian hoàn tiền của khách lên tới 24h."
3. Bottleneck,"Bước 5 (mất 15–30 phút/vụ): Đối soát dữ liệu phân tán (GPS, dwell time, viễn thông GSM, in-app chat, lịch sử tài xế) hoàn toàn thủ công trên giao diện CRM/Admin để xác định hành vi gian lận trước khi duyệt đền bù hoặc khóa tài xế."
4. Business Impact,"Thất thoát chi phí đền bù đơn hàng và cấp voucher bồi thường; gây quá tải tổng đài CSKH vào khung giờ cao điểm ăn trưa/tối; tạo bức xúc tột cùng cho khách hàng do vừa bị mất bữa ăn vừa phải chờ đợi lâu, làm suy giảm uy tín thương hiệu và tụt giảm tỷ lệ giữ chân khách hàng (retention rate) của mảng ẩm thực Xanh SM."
5. Success Metric,"1. Giảm 60% tỷ lệ khiếu nại phát sinh từ lỗi ""khách không nhận được đơn đồ ăn"" nhờ cơ chế chặn sớm (Prevention).2. Rút ngắn thời gian xác minh và phê duyệt bồi hoàn cho khách từ 24h xuống dưới 3 phút (Efficiency)."
6. Operational Boundary,"Hệ thống Rule Engine trên thiết bị/Client được tự động chặn nút hủy đơn nếu chưa thỏa điều kiện gọi GSM hoặc dwell time < 3 phút. AI/LLM được quyền trích xuất dữ liệu đa nguồn (GPS, call log, chat, lịch sử tài xế) để tính toán điểm rủi ro (Fraud Score) và tạo sẵn quyết định bồi thường dạng nháp (Draft Resolution). CẤM: AI không được tự ý thực hiện hành động khóa tài khoản vĩnh viễn đối với tài xế mà không có xác nhận từ Ops cấp quản lý; không được từ chối bồi hoàn cho khách VIP nếu chưa qua bước rà soát thủ công của nhân sự (HITL)."

7. 3.3. Future-State Flow & AI Fit
8. AI Fit: Kết hợp Client Rule Engine (chặn cứng vi phạm quy trình ở thời gian thực) và LLM / Fraud Analytics Feature (tổng hợp log phi cấu trúc, đánh giá Fraud Score và soạn sẵn đề xuất bồi thường). Không sử dụng Agent tự trị hoàn toàn nhằm tránh rủi ro tự động phạt nhầm tài xế hoặc từ chối bồi thường sai đối với khách hàng thực sự gặp sự cố.

Quy trình tương lai (Future-State):
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Tài xế đến   │     │ 🔵 Real-time │     │ Khách báo    │     │ 🟢 CSKH xác  │
│ điểm & chuẩn │ ──→ │ Rule Check:  │ ──→ │ chưa nhận:   │ ──→ │ nhận (1 click│
│ bị bấm hủy   │     │ Chặn nếu sai │     │ 🔵 AI tính   │     │ duyệt đền bù │
│              │     │ GSM/GPS dừng │     │ Fraud Score  │     │ & phạt tx)   │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      │
                                                                      ▼
                                                               ↩️ Fallback:
                                                               Nếu Fraud Score
                                                               nằm ở vùng xám
                                                               (40% - 70%), chuyển
                                                               Ops đối soát tay
                                                               theo quy trình cũ.
