# 01 — Problem Scan (Cá nhân)

- **Họ tên:** Huỳnh Văn Cường  
- **MSSV:** 2A202603866  
- **Vai trò giả định:** AI Product Engineer @ Vin Smart Future  

---

# Phase 1 — SCAN (≥5 bài toán)

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên xử lý thủ công sự cố hết pin / sạc thực địa: tra vị trí xe, tìm trạm trống, soạn tin chỉ dẫn (~15 phút/lượt). |
| 2 | Xanh SM | Lặp lại | Phân loại và tóm tắt lý do hủy chuyến từ ghi chú tài xế + log cuộc gọi để tìm pattern lỗi điều vận. |
| 3 | VinFast | Lặp lại | Đối soát hóa đơn sạc điện tuần giữa app tài xế / đối tác trạm và hệ thống kế toán nội bộ. |
| 4 | Vinhomes | AI-upgrade | CSKH App Resident trả lời khiếu nại phí/ tiện ích chậm, nội dung rập khuôn, thiếu ngữ cảnh căn hộ. |
| 5 | Vinmec | Pain từ người khác | Bác sĩ mất 20–30 phút/ca viết tóm tắt xuất viện từ hồ sơ phân tán. |
| 6 | Vinpearl | Tốn thời gian | Điều phối slot check-in / xếp lịch trải nghiệm khu vui chơi khi khách đổi lịch sát giờ. |

---

# Phase 2 — QUICK-ASSESS (Top 3 cards)

## Card #1 — Xanh SM: Sự cố hết pin thực địa

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│ Bài toán: Tài xế báo hết pin giữa đường; dispatcher phải    │
│ tìm trạm/cứu hộ và soạn chỉ dẫn thủ công.                   │
│ Công ty: [x] Xanh SM                                        │
│ Actor: Tài xế + Điều phối viên                              │
│ Workflow: gọi tổng đài → tra GPS → tìm trạm trống → soạn SMS│
│           → gọi cứu hộ nếu pin cực thấp                     │
│ Bottleneck: tìm trạm + soạn tin (⏱ ~10–12 phút)             │
│ AI nhảy vào: draft chỉ dẫn / đề xuất cứu hộ theo ngưỡng pin │
│ Metric: giảm xử lý 15 phút → dưới 3 phút                    │
│ Architecture: [x] LLM Feature (+ rule ngưỡng pin)           │
└─────────────────────────────────────────────────────────────┘
```

## Card #2 — Vinhomes: CSKH Resident

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│ Bài toán: Ticket khiếu nại trên App Resident chậm phản hồi, │
│ nội dung generic, thiếu ngữ cảnh căn hộ/phí.                │
│ Công ty: [x] Vinhomes                                       │
│ Actor: CSKH + Cư dân                                        │
│ Workflow: nhận ticket → đọc lịch sử → soạn trả lời → duyệt  │
│ Bottleneck: soạn trả lời có ngữ cảnh (⏱ ~8–12 phút/ticket)  │
│ AI nhảy vào: draft trả lời + gợi ý rule routing             │
│ Metric: P50 thời gian phản hồi lần 1 từ 12h → dưới 2h       │
│ Architecture: [x] LLM Feature (HITL bắt buộc)               │
└─────────────────────────────────────────────────────────────┘
```

## Card #3 — Vinmec: Tóm tắt xuất viện

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│ Bài toán: Bác sĩ viết tóm tắt xuất viện thủ công từ nhiều   │
│ nguồn hồ sơ, mất 20–30 phút/bệnh nhân.                      │
│ Công ty: [x] Vinmec                                         │
│ Actor: Bác sĩ điều trị                                      │
│ Workflow: đọc HSBA → chép xét nghiệm → viết tóm tắt → ký    │
│ Bottleneck: tổng hợp narrative lâm sàng                     │
│ AI nhảy vào: draft tóm tắt từ structured fields             │
│ Metric: giảm thời gian viết từ 25 phút → dưới 8 phút        │
│ Architecture: [x] LLM Feature (bác sĩ duyệt 100%)           │
└─────────────────────────────────────────────────────────────┘
```

---

## Lựa chọn Deep-Dive

Chọn **Card #1 — Xanh SM sự cố hết pin thực địa** vì:
- Có bottleneck rõ, metric đo được, khớp prototype code (ngưỡng pin + `[DRAFT_ONLY]`).
- Card #2 rủi ro pháp lý/phí cư dân cao hơn → cần thêm baseline.
- Card #3 liên quan dữ liệu y tế nhạy cảm → scope HITL/compliance nặng hơn cho 1 buổi lab.
