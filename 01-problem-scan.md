# Phase 1-2 — Problem Scan: Green SM Food Complaint Verification

> Bài scan này do cá nhân thực hiện và được dùng làm đầu vào cho Phase 3 nhóm. Các số liệu thời gian trong Quick Cards là **giả định làm việc**, chưa phải số liệu vận hành chính thức của Green SM Food.

---

# Phase 1 — SCAN: Tìm kiếm cơ hội AI

Tôi sử dụng **4 Lenses** để quét các quy trình vận hành trong hệ sinh thái Vingroup:

1. **Lặp lại** — tác vụ xử lý nhiều lần mỗi ngày.
2. **Tốn thời gian** — tác vụ thủ công chiếm nhiều thời gian nhân viên.
3. **AI có thể tốt hơn** — quy trình chậm, rập khuôn hoặc cần xử lý ngôn ngữ tự nhiên.
4. **Pain từ người khác** — pain point làm khách hàng, tài xế hoặc nhân viên vận hành không hài lòng.

## Bảng SCAN

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---:|---|---|---|
| 1 | Green SM Food | Pain từ người khác | Khách hàng phản ánh chưa nhận được đồ ăn dù ứng dụng hiển thị đơn đã giao thành công. CSKH phải đối soát trạng thái app, GPS, ảnh giao hàng, lịch sử liên hệ tài xế và xác nhận nhà hàng. |
| 2 | Vinhomes | Tốn thời gian | Nhân viên CSKH mất nhiều thời gian đọc, phân loại và soạn phản hồi cho lượng lớn khiếu nại cư dân về phí dịch vụ, vệ sinh, bảo trì và tiện ích. |
| 3 | VinFast | Lặp lại | Nhân viên hậu mãi phải đối chiếu thông tin xe, lịch sử bảo dưỡng, mô tả lỗi và điều kiện bảo hành trước khi chuyển yêu cầu đến bộ phận kỹ thuật. |
| 4 | Xanh SM | Stakeholder Pain | Tài xế và điều phối viên khó xác định nguyên nhân khách hủy chuyến khi dữ liệu nằm rải rác ở ghi chú chuyến đi, phản hồi khách và lịch sử hệ thống. |
| 5 | Vinmec | AI có thể tốt hơn | Việc tổng hợp hồ sơ bệnh án, xét nghiệm và ghi chú lâm sàng để soạn tóm tắt xuất viện vẫn tốn nhiều thời gian và cần truy xuất thông tin nhanh hơn. |

---

# Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Từ năm bài toán trên, tôi chọn ba bài toán có tính khả thi và giá trị nghiệp vụ cao nhất để phân tích nhanh.

## Quick Problem Card #1 — Green SM Food Complaint Verification

```text
Bài toán: Khách hàng phản ánh chưa nhận được đồ ăn dù ứng dụng
          hiển thị đơn đã giao thành công.

Công ty:  [x] Green SM Food

Actor:    Khách hàng, nhân viên CSKH/Ops, tài xế, nhà hàng.

Workflow thủ công:
1. Khách gửi khiếu nại.
2. CSKH tra cứu mã đơn và trạng thái giao hàng.
3. CSKH mở nhiều nguồn dữ liệu: GPS, ảnh, lịch sử liên hệ,
   xác nhận nhà hàng.
4. CSKH/Ops liên hệ tài xế và/hoặc nhà hàng để xác minh.
5. Nhân viên có thẩm quyền quyết định hoàn tiền, giao bù hoặc
   từ chối khiếu nại.

Bottleneck: Bước 2-4.
Thời gian giả định: 14-20 phút/vụ việc, cần xác minh thực tế.

AI hỗ trợ:
- Đọc và phân loại khiếu nại.
- Trích xuất dữ liệu đơn hàng.
- Tổng hợp bằng chứng có sẵn và chỉ ra dữ liệu còn thiếu.
- Đề xuất bước xác minh tiếp theo cho CSKH.

Metric đề xuất:
- Giảm thời gian phân loại và đối soát ban đầu từ 14 phút
  xuống dưới 7 phút/vụ.
- Độ chính xác phân loại khiếu nại >= 90%.
- Tỷ lệ cảnh báo sai <= 5% trên tập dữ liệu có nhãn.

Quick Architecture: [x] Rule  [x] LLM Feature  [ ] Agentic Loop
```

## Quick Problem Card #2 — Vinhomes Resident Complaint Triage

```text
Bài toán: Nhân viên CSKH mất nhiều thời gian đọc, phân loại
          và soạn phản hồi cho khiếu nại cư dân.

Công ty:  [x] Vinhomes

Actor:    Cư dân, nhân viên CSKH, bộ phận kỹ thuật và quản lý
          vận hành tòa nhà.

Workflow thủ công:
1. Cư dân gửi khiếu nại qua app hoặc tổng đài.
2. Nhân viên đọc và phân loại vấn đề.
3. Tra cứu quy định phí, dịch vụ hoặc lịch bảo trì.
4. Chuyển yêu cầu đến bộ phận phụ trách.
5. Soạn và gửi phản hồi cho cư dân.

Bottleneck: Bước 2-4.
Thời gian giả định: 8-12 phút/lượt, cần xác minh thực tế.

AI hỗ trợ:
- Phân loại khiếu nại.
- Trích xuất thông tin căn hộ, thời gian và loại sự cố.
- Soạn bản nháp phản hồi cho nhân viên duyệt.

Metric đề xuất:
- Giảm thời gian phân loại và chuẩn bị phản hồi xuống dưới
  3 phút/lượt.
- Độ chính xác phân loại >= 90%.

Quick Architecture: [x] Rule  [x] LLM Feature  [ ] Agentic Loop
```

## Quick Problem Card #3 — VinFast Warranty Document Assistant

```text
Bài toán: Nhân viên hậu mãi mất thời gian đối chiếu thông tin xe,
          lịch sử bảo dưỡng và điều kiện bảo hành trước khi tiếp
          nhận yêu cầu bảo hành.

Công ty:  [x] VinFast

Actor:    Khách hàng, nhân viên hậu mãi, cố vấn dịch vụ, bộ phận
          kỹ thuật.

Workflow thủ công:
1. Khách gửi yêu cầu bảo hành.
2. Nhân viên tiếp nhận hồ sơ.
3. Tra cứu thông tin xe và lịch sử bảo dưỡng.
4. Đối chiếu điều kiện bảo hành.
5. Chuyển hồ sơ cho bộ phận kỹ thuật xét duyệt.

Bottleneck: Bước 2-4.
Thời gian giả định: 10-20 phút/lượt, cần xác minh thực tế.

AI hỗ trợ:
- Trích xuất thông tin từ tài liệu bảo hành.
- Tìm điều khoản liên quan.
- Đánh dấu dữ liệu còn thiếu cho nhân viên kiểm tra.

Metric đề xuất:
- Giảm thời gian chuẩn bị hồ sơ xuống dưới 7 phút/lượt.
- Độ chính xác trích xuất thông tin >= 95%.

Quick Architecture: [x] Rule  [x] LLM Feature  [ ] Agentic Loop
```

---

# Quyết định chọn bài toán cho Deep-Dive

Nhóm chọn **Card #1 — Green SM Food Complaint Verification** để thực hiện Deep-Dive ở Phase 3.

Lý do chọn:

- Bài toán có tác động trực tiếp đến khách hàng và tài xế.
- Có thể bắt đầu bằng phạm vi hẹp: xác minh khiếu nại "đã giao nhưng khách nói chưa nhận hàng".
- Có thể dùng Rule + LLM Feature thay vì Agentic Loop, phù hợp với yêu cầu human review.
- Có thể thiết kế rõ Operational Boundary: AI hỗ trợ tổng hợp bằng chứng, không tự kết luận gian lận và không tự duyệt hoàn tiền.

Lý do loại các card khác:

- **Card #2 — Vinhomes Complaint Triage:** khả thi, nhưng rủi ro tranh chấp phí và quy định cư dân cần thêm dữ liệu nội bộ; nên làm sau khi có quy trình rõ hơn.
- **Card #3 — VinFast Warranty Assistant:** giá trị cao, nhưng phụ thuộc nhiều vào quy định bảo hành và dữ liệu xe; cần truy cập dữ liệu chính thức trước khi thử nghiệm.
e (back-office), không ảnh hưởng trực tiếp đến hiệu suất vận hành thời gian thực (real-time) như sự cố hết pin của tài xế trên đường đón khách.

---