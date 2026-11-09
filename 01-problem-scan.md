# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | Vinmec | AI Advantage | Người bệnh mô tả triệu chứng mơ hồ khiến điều phối viên xếp sai chuyên khoa (chiếm ~15%), gây lãng phí 60–90 phút khám lại và làm tắc nghẽn lịch bác sĩ.
| 2 | Vinpearl| User Paint Points | Phản ánh sự cố phân tán rải rác và trễ 4–6 tiếng đến cuối ca mới tổng hợp, làm lỡ "thời điểm vàng" xử lý tại chỗ và dẫn đến review 1-sao công khai.  |
| 3 | Vinhomes| Time-consuming|Kỹ sư BQL mất 120–150 phút/bộ để đọc và đối chiếu thủ công thuyết minh thi công với quy chuẩn, khiến thời gian cấp phép căn hộ kéo dài 5–7 ngày. |
| 4 |Vinfast |AI Advantage |Đội ngũ kỹ thuật mất 3–5 ngày/tuần để phân loại thủ công hàng nghìn phản hồi văn bản tự do, làm chậm tiến độ phát hiện lỗi phần mềm (BMS/Infotainment). |
| 5 | Xanh SM | User Pain Points | Nhân viên CSKH mất 25–35 phút/ticket để đối soát ảnh POD, GPS và log gọi, khiến quy trình giải quyết khiếu nại đơn không thành công bị kéo dài 24–48 giờ. |   

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3** từ danh sách SCAN: #1 (Điều phối bệnh nhân Vinmec), #3 (Cấp phép hồ sơ thi công căn hộ Vinhomes), #5 (Xác minh và xử lý khiếu nại giao hàng )

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #01                                      │
│                                                             │
│ Bài toán (1 câu): Định tuyến sai chuyên khoa khám ban đầu   │
│ do mô tả triệu chứng mơ hồ, gây lãng phí giờ khám của bác sĩ│
│ và kéo dài thời gian chờ đợi của người bệnh.                │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [X] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   • Người bệnh: Chờ lâu, mất thêm 60-90 phút/lượt khám lại. │
│   • Điều phối viên: Áp lực KPI cuộc gọi, thiếu chuyên môn y.│
│   • Bác sĩ chuyên khoa sâu: Lãng phí 20-30% ca khám lệch.   │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Nhận mô tả tự do qua App/Hotline (⏱ 2-3 phút) ──>      │
│   2. ĐPV tự suy đoán và chọn chuyên khoa (⏱ 5-8 phút) ──>   │
│   3. Tạo lịch hẹn và cấp số thứ tự phòng khám (⏱ 1-2 phút) ─>│
│   4. Bác sĩ khám lâm sàng, phát hiện sai khoa (⏱ 15-20 phút)─>│
│   5. Làm thủ tục đổi khoa và xếp hàng chờ lại (⏱ 45-60 phút)│
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2: Phán đoán khoa     │
│ (⏱ 5-8 phút/cuộc gọi; tỷ lệ định tuyến sai chiếm 15%).     │
│ Gây lãng phí dây chuyền 60-90 phút ở bước 4 và 5.           │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2: Dynamic       │
│ Triage Agent hỏi thích ứng 3-4 câu theo cây phân loại lâm   │
│ sàng để gợi ý đúng chuyên khoa và gắn cờ ca cấp cứu.        │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   • Giảm tỷ lệ chuyển khoa chéo trong ngày từ 15% ──> < 3%. │
│   • Rút ngắn thời gian tiếp nhận đặt lịch từ 8 min ──> < 2 min│
│   • Tiết kiệm 45-60 phút chờ đợi trung bình cho bệnh nhân.  │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [ ] LLM  [X] Agent │
└─────────────────────────────────────────────────────────────┘

```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #02                                      │
│                                                             │
│ Bài toán (1 câu): Kỹ sư Ban Quản lý phải rà soát thủ công   │
│ từng trang hồ sơ thuyết minh thi công căn hộ, gây tồn đọng  │
│ cấp phép và tiềm ẩn nguy cơ sai phạm kỹ thuật.              │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [X] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   • Kỹ sư BQL: Quá tải đọc tài liệu PDF dài 50-100 trang.   │
│   • Chủ nhà & Nhà thầu: Chờ cấp phép 5-7 ngày làm việc, trễ │
│     tiến độ khởi công và phát sinh chi phí nhân công.       │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Nhà thầu nộp thuyết minh & bản vẽ qua cổng (⏱ 5 phút) ─>│
│   2. Kỹ sư đọc từng trang, tra cứu quy chuẩn (⏱ 90-120 phút)─>│
│   3. Lập danh sách lỗi vi phạm: MEP, tải sàn (⏱ 30-45 phút)─>│
│   4. Soạn văn bản trả kết quả hoặc yêu cầu sửa (⏱ 20 phút). │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3: Đọc và đối     │
│ chiếu quy chuẩn (⏱ 120-165 phút/bộ hồ sơ; thời gian tồn đọng│
│ toàn trình kéo dài 5-7 ngày làm việc).                      │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3: RAG       │
│ pipeline tự bóc tách thông số kỹ thuật, đối chiếu sổ tay quy│
│ chuẩn tòa nhà và tự sinh báo cáo vi phạm kèm trích dẫn.     │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   • Giảm thời gian kỹ sư rà soát từ 150 phút ──> < 15 phút. │
│   • Rút ngắn thời gian trả kết quả cấp phép từ 5 ngày ──> < 24h│
│   • Độ chính xác phát hiện điều khoản vi phạm đạt > 95%.    │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [X] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #03                                      │
│                                                             │
│ Bài toán (1 câu): Quy trình xác minh và đối soát khiếu nại  │
│ giao hàng không thành công/bị chiếm đoạt diễn ra thủ công,  │
│ gây tắc nghẽn xử lý và kéo dài thời gian đền bù cho khách.  │
│ Công ty thành viên: [ ] VinFast  [X] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   • Khách hàng: Mất hàng/mất bữa ăn, ức chế vì chờ đợi bồi  │
│     thường kéo dài từ 24 đến 48 giờ.                        │
│   • Nhân viên CSKH/Ops: Mất 15-25 phút/ticket để tra soát   │
│     thủ công qua 3-4 màn hình hệ thống riêng biệt.          │
│   • Tài xế: Bị phong tỏa ví tiền thưởng trong nhiều ngày    │
│     khi đang chờ kết quả xác minh đơn hàng.                 │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Khách gửi ticket khiếu nại chưa nhận được hàng ──>     │
│   2. CSKH gom dữ liệu: xin ảnh POD, trích log cuộc gọi &    │
│      tọa độ GPS của chuyến đi (⏱ 10-15 phút/ca) ──>          │
│   3. CSKH xem ảnh giao hàng, soi tọa độ và thời gian dừng xe│
│      để tìm dấu hiệu bất thường (⏱ 15-20 phút/ca) ──>        │
│   4. Ra quyết định bồi thường và gõ phản hồi (⏱ 5-10 phút). │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3: Đối soát đa    │
│ nguồn để xác minh gian lận (⏱ 25-35 phút thao tác/ticket;   │
│ tổng thời gian đóng khiếu nại tồn đọng mất 24 - 48 giờ).    │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3:           │
│ Multimodal LLM tự động thẩm định ảnh bằng chứng (phát hiện  │
│ ảnh chụp sàn/ảnh đen/không có bối cảnh), đối chiếu log gọi  │
│ GSM và tọa độ GPS, sinh điểm nghi vấn (Fraud Score) và đề   │
│ xuất quyết định bồi thường ngay trên màn hình CSKH.         │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   • Rút ngắn thời gian xác minh/đối soát từ 25 min ──> < 1 min│
│   • Giảm tổng thời gian giải quyết khiếu nại từ 24-48h ──> < 1h│
│   • Tự động xử lý sơ bộ hợp lệ cho > 80% số lượng ticket.   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [X] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---