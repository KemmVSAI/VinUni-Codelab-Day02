# Deliverable Report — Vin Smart Future (GSM / Xanh SM Use Case)

> **Báo cáo hoàn chỉnh từ đầu đến cuối lab, tuân thủ chặt chẽ Rubric đánh giá và bối cảnh vận hành thực tế của Vin Smart Future.**
> 
> * **Người thực hiện:** **Dũng** — AI Product Engineer tại **Vin Smart Future**.
> * **Mảng kinh doanh lựa chọn:** **GSM (Xanh SM) — Vận hành đội xe taxi điện thông minh & Điều phối cứu hộ năng lượng.**

---

## 🏛️ Bối cảnh: Tôi là ai?

Tôi là **Dũng**, AI Engineer tại **Vin Smart Future**. Nhóm chúng tôi được phân công làm việc trực tiếp với Trung tâm Điều hành Vận tải của **Xanh SM (GSM)** nhằm rà soát các điểm nghẽn (bottlenecks) trong vận hành đội xe điện và xây dựng các giải pháp AI tạo sinh (Generative AI) có kiểm soát.

Trong quá trình ngồi cùng ca trực (shadowing) với các điều phối viên (Dispatchers) tại Trung tâm Điều vận Xanh SM vào khung giờ cao điểm (17h00 – 19h30), tôi nhận thấy áp lực xử lý các cuộc gọi/tin nhắn báo cạn pin khẩn cấp là cực kỳ lớn. Việc phải tra cứu chéo giữa nhiều màn hình và gõ tay từng tin nhắn chỉ dẫn vừa làm chậm thời gian giải cứu xe, vừa tiềm ẩn rủi ro điều hướng xe pin yếu đi quá xa gây sập nguồn giữa đường. Bài toán tôi đề xuất và xây dựng bản mẫu (prototype) ngày hôm nay xuất phát từ thực tế vận hành cấp bách đó.

---

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Dùng **4 Lenses** quét qua vận hành của các công ty thành viên Vingroup.

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Lặp lại | Tài xế cố tình lách quy trình giao đồ ăn (chỉ nháy máy/nhắn qua app rồi tự ý hủy hoặc chiếm đoạt đơn), dẫn đến thất thoát đơn hàng và khiếu nại nghiêm trọng từ khách.|
| 2 | **Xanh SM** | Tốn thời gian | Điều phối viên xử lý thủ công các yêu cầu hỗ trợ khẩn cấp từ tài xế về sự cố cạn pin thực địa, tra cứu trụ sạc trống và điều xe cứu hộ pin (mất 12–15 phút/lượt). |
| 3 | **VinFast** | Lặp lại | Thẩm định log lỗi trạm sạc và đối chiếu hóa đơn sạc điện để xử lý yêu cầu hoàn tiền cho chủ xe hằng ngày. |
| 4 | **Vinhomes** | AI-upgrade | Phân loại mức độ khẩn cấp và điều phối tự động các phản ánh kỹ thuật (điện, nước, thang máy) kèm hình ảnh của cư dân trên App Vinhomes Resident (hiện CSKH phản hồi mẫu rập khuôn, mất 8–12 tiếng). |
| 5 | **Vinmec** | Pain từ người khác | Bác sĩ và chuyên viên bảo hiểm mất quá nhiều thời gian tổng hợp bệnh sử và điền hồ sơ bảo lãnh viện phí theo form riêng của từng hãng bảo hiểm (mất 20–25 phút/ca, gây ùn ứ quầy xuất viện). |
| 6 | **Vinpearl** | AI-upgrade | Trợ lý đa ngôn ngữ hỗ trợ du khách quốc tế tự động đổi lịch trình vui chơi/ẩm thực khi các khu vực ngoài trời tại VinWonders tạm đóng cửa do thời tiết xấu. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Chọn top 3 từ danh sách SCAN: **#1 (Gian lận đơn hàng), #4 (Vinhomes Phản ánh kỹ thuật), #5 (Vinmec Bảo lãnh viện phí).**

## Thẻ bài toán tiêu biểu 1: Card #1 — Chống gian lận chiếm đoạt đơn hàng do cố tình không liên lạc khách

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #01 (Delivery Fraud & Misdelivery)       │
│                                                             │
│ Bài toán (1 câu): Tài xế cố tình lách quy trình giao đồ ăn  │
│ bằng cách chỉ gọi/nhắn qua app rồi tự ý hủy/chiếm đoạt đơn, │
│ gây mất đơn hàng và bức xúc tột cùng cho khách hàng.        │
│ Công ty thành viên: [ ] VinFast  [X] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   • Khách hàng: Bị đói, mất bữa ăn, chờ cả tiếng mới biết   │
│     đơn bị hủy, cảm giác bị lừa đảo.                        │
│   • Đội ngũ CSKH/Ops: Quá tải giải quyết ticket khiếu nại,  │
│     phải bù tiền đền đơn hoặc voucher cho khách.            │
│   • Uy tín dịch vụ Xanh SM: Tụt giảm tỷ lệ giữ chân khách.  │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Tài xế đến điểm giao, chỉ nhắn tin/nháy máy qua app ──> │
│   2. Chờ 5-10 phút (khách không bật thông báo app) ──>      │
│   3. Tài xế bấm "Khách không nghe máy" / "Đã hoàn thành" ──>│
│   4. Khách mở app kiểm tra thấy đơn đã xong dù chưa nhận ──> │
│   5. Khách gọi hotline khiếu nại ──> CSKH đối soát thủ công │
│      log cuộc gọi/GPS để xác minh và xử lý bồi thường.       │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 5: Tiếp nhận khiếu nại│
│ và đối soát điều tra gian lận (⏱ 15 - 30 phút/vụ việc).     │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                        │
│   • Bước 2 & 3: Giám sát hành vi real-time (Intervention).  │
│     Nếu phát hiện tài xế chuẩn bị bấm "Không giao được" mà  │
│     chưa có cuộc gọi GSM thành công ra số điện thoại khách  │
│     hoặc thời gian dừng xe (dwell time) tại tọa độ giao < 3 │
│     phút ──> Tự động chặn và kích hoạt gọi tự động (IVR).   │
│   • Bước 5: Đánh giá xác suất gian lận tự động (Fraud Score)│
│     dựa trên lịch sử hủy đơn, thời gian dừng, log GPS và chat│
│     để tự động duyệt hoàn tiền ngay cho khách.              │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   "Giảm 60% tỷ lệ khiếu nại đơn đồ ăn không nhận được;      │
│    Rút ngắn thời gian xử lý khiếu nại từ 24h ──> dưới 3 phút"│
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [X] LLM  [ ] Agent │
│ (Kết hợp Rule chặn cứng ở Client + LLM/ML phân tích log)   │
└─────────────────────────────────────────────────────────────┘

Thẻ bài toán 2: Card #4 — Vinhomes Phân loại & Điều phối Ticket Kỹ thuật
Text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #4                                       │
│                                                             │
│ Bài toán: Nhân viên BQL Vinhomes quá tải khi phải đọc, xem  │
│ ảnh và phân loại thủ công hàng nghìn ticket kỹ thuật/ngày.  │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau? Nhân viên CSKH BQL Tòa nhà & Cư dân Vinhomes   │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi ticket kèm ảnh + mô tả lỗi lên App Resident │
│   → 2. Nhân viên BQL mở từng ticket, xem ảnh & đánh giá lỗi │
│   → 3. Gán phiếu việc thủ công cho đội kỹ thuật (M&E/PCCC)  │
│   → 4. Soạn tin nhắn phản hồi xác nhận thời gian xử lý (SLA)│
│                                                             │
│ Bước nào tốn nhất? Bước 2-4 (⏱ 8 phút/lượt)                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 4         │
│ (Phân tích ảnh/văn bản -> Gắn nhãn P1-P4 -> Draft phản hồi) │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian phân loại & điều phối từ 8 phút ──> < 1 phút.│
│                                                             │
│ Quick Architecture: [x] LLM Feature (Multimodal Triage)     │
└─────────────────────────────────────────────────────────────┘
Thẻ bài toán 3: Card #5 — Vinmec Trích xuất & Lập Hồ sơ Bảo lãnh Viện phí
Text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #5                                       │
│                                                             │
│ Bài toán: Chuyên viên bảo lãnh viện phí Vinmec mất nhiều    │
│ thời gian tóm tắt bệnh sử và điền form của từng hãng BH.    │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau? Chuyên viên Bảo lãnh, Bác sĩ & Bệnh nhân       │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Tiếp nhận thẻ bảo hiểm và yêu cầu xuất viện của BN     │
│   → 2. Tra cứu lịch sử khám, xét nghiệm trên phần mềm HIS   │
│   → 3. Đối chiếu điều khoản loại trừ & điền form bảo hiểm   │
│   → 4. Bác sĩ kiểm tra lại toàn bộ, ký xác nhận và gửi đi   │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 20 phút/lượt)                │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3            │
│ (Tóm tắt hồ sơ y khoa từ HIS -> Điền sẵn biểu mẫu bảo lãnh) │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ Giảm thời gian chuẩn bị hồ sơ từ 25 phút ──> dưới 4 phút.   │
│                                                             │
│ Quick Architecture: [x] LLM Feature (RAG + Document Draft)  │
└─────────────────────────────────────────────────────────────┘
