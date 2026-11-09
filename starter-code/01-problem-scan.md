# Lab 02 — File 01: Problem Scan & Quick-Assess (Vin Smart Future)

## 🔍 Phase 1 — SCAN (Quét cơ hội bài toán thực tế tại Vingroup)

Dưới đây là 5 bài toán/điểm nghẽn vận hành thực tế tại các công ty thành viên Vingroup được xác định thông qua 4 Lenses (**Repetitive**, **Time-consuming**, **AI-upgrade**, **Stakeholder Pain**):

| # | Subsidiary (Công ty thành viên) | Lens | Mô tả ngắn bài toán |
| :--- | :--- | :--- | :--- |
| **1** | **Xanh SM (GSM)** | **Stakeholder Pain & Time-consuming** | **Điều phối cứu hộ pin khẩn cấp:** Tài xế taxi điện báo cạn pin (< 10%) giờ cao điểm; điều phối viên phải tra cứu thủ công vị trí GPS, khoảng cách trạm sạc trống và soạn tin nhắn hướng dẫn hoặc gọi xe sạc lưu động, dễ dẫn đến quyết định sai khiến xe sập nguồn giữa đường. |
| **2** | **Vinhomes** | **Repetitive & Time-consuming** | **Phân loại và xử lý phản ánh kỹ thuật của cư dân (Vinhomes Resident App):** Ban Quản lý (BQL) nhận hàng nghìn ticket/ngày kèm ảnh chụp và mô tả tự do (rò nước, hỏng thang máy, ồn ào); nhân viên trực phải đọc, phân loại mức độ khẩn cấp và gán thủ công cho từng đội kỹ thuật (M&E, Cảnh quan, An ninh). |
| **3** | **VinFast** | **Time-consuming & Repetitive** | **Thẩm định yêu cầu bảo hành & đối soát lỗi trạm sạc:** Kỹ thuật viên xưởng dịch vụ và bộ phận CSKH phải đọc log lỗi từ xe/trạm sạc V-Green, đối chiếu thủ công với sổ tay chính sách bảo hành pin/xe hàng trăm trang và hóa đơn sạc để duyệt hoàn tiền hoặc thay thế linh kiện. |
| **4** | **Vinmec** | **Time-consuming & Stakeholder Pain** | **Tóm tắt bệnh án lịch sử & Chuẩn bị hồ sơ bảo lãnh viện phí:** Bác sĩ và nhân viên bảo hiểm mất 15–25 phút/ca để đọc lại toàn bộ lịch sử khám đa chuyên khoa, kết quả xét nghiệm cũ và trích xuất thông tin y khoa theo mẫu của từng hãng bảo hiểm tư nhân. |
| **5** | **Vinpearl / VinWonders** | **AI-upgrade & Stakeholder Pain** | **Trợ lý xử lý khiếu nại đa ngôn ngữ và thiết kế lại lịch trình khi thời tiết xấu:** Khi mưa bão tại Phú Quốc/Nha Trang làm đóng cửa cáp treo hoặc trò chơi ngoài trời, quầy CSKH bị quá tải bởi du khách quốc tế (Hàn, Trung, Nga, Anh) yêu cầu đổi lịch trình, hoàn/đổi dịch vụ spa, ẩm thực trong khu nghỉ dưỡng. |

---

## 🃏 Phase 2 — QUICK-ASSESS (3 Quick Problem Cards)

### 📌 QUICK PROBLEM CARD #1 — Xanh SM (Điều phối cứu hộ pin khẩn cấp)

```text
┌────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                      │
│                                                                            │
│ Bài toán (1 câu): Điều phối viên Xanh SM mất nhiều thời gian xử lý thủ công│
│ các ca taxi điện báo pin yếu/nguy kịch giờ cao điểm, dễ chỉ nhầm trạm sạc  │
│ quá xa khiến xe hết sạch pin giữa đường gây ùn tắc và mất doanh thu.       │
│                                                                            │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes                 │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________                 │
│                                                                            │
│ Ai đang đau (Actor)? Điều phối viên vận hành (Dispatcher) & Tài xế Xanh SM │
│                                                                            │
│ Workflow thủ công hiện tại (4 bước):                                       │
│   1. Tài xế nhắn/gọi báo tọa độ GPS và % pin còn lại về tổng đài           │
│   ──> 2. Điều phối viên mở bản đồ, tra cứu trạm sạc V-Green gần nhất,      │
│          ước lượng quãng đường và kiểm tra điều kiện an toàn (<5% pin)     │
│   ──> 3. Nếu pin <5% hoặc trạm >5km, liên hệ đội xe sạc cứu hộ lưu động    │
│   ──> 4. Soạn tin nhắn hướng dẫn lộ trình/phương án xử lý gửi cho tài xế   │
│                                                                            │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & Bước 4 (⏱ 6 - 8 phút/lượt)       │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3, 4: Tự động kiểm tra ngưỡng│
│ an toàn (<5% pin, bán kính 5km), tạo lệnh JSON điều xe cứu hộ lưu động và  │
│ soạn sẵn tin nhắn nháp gắn nhãn [DRAFT_ONLY] để điều phối viên duyệt gửi.  │
│                                                                            │
│ Đo thành công bằng gì (Metric có số)?                                      │
│ - Giảm thời gian phản hồi trung bình mỗi ca từ 7 phút ──> dưới 45 giây.    │
│ - Giảm tỷ lệ điều hướng sai (gửi xe <5% pin đi trạm >5km) xuống 0%.        │
│                                                                            │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM (kết hợp Rule Guardrails) │
│                     [ ] Agent                                              │
└────────────────────────────────────────────────────────────────────────────┘
