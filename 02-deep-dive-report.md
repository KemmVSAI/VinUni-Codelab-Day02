# 🏗️ Phase 3 — DEEP-DIVE: Xác minh khiếu nại giao hàng Xanh SM

> **Bài toán được chọn:** Xxác minh và đối soát khiếu nại giao hàng không thành công/bị chiếm đoạt.


## 3.1. Current-State Workflow Mapping

**Quy trình hiện tại:** Khách gửi khiếu nại chưa nhận được hàng → CSKH/Ops thu thập bằng chứng từ nhiều hệ thống → đối chiếu thủ công → quyết định bồi thường và phản hồi khách hàng.

```text
┌────────────────────┐   🔄    ┌────────────────────────┐   🔄    ┌────────────────────────┐
│ 1. Tiếp nhận       │ ──────→ │ 2. Gom dữ liệu         │ ──────→ │ 3. Đối soát bằng chứng │
│ khiếu nại          │         │ 🔴 10–15 phút          │         │ 🔴 15–20 phút          │
│                    │         │ CSKH lấy ảnh POD,      │         │ CSKH xem ảnh, đối chiếu│
│ Khách báo chưa     │         │ log cuộc gọi và GPS    │         │ vị trí/thời gian dừng  │
│ nhận được hàng     │         │ từ 3–4 màn hình riêng │         │ xe, tìm dấu hiệu bất   │
│ Thời lượng: chưa   │         │ biệt                   │         │ thường                 │
│ có baseline        │         │ Out: bộ bằng chứng    │         │ Out: nhận định vụ việc │
└────────────────────┘         └────────────────────────┘         └────────────────────────┘
                                                                           │
                                                                           ▼
┌────────────────────┐   🔄    ┌────────────────────────┐
│ 5. Theo dõi        │ ←────── │ 4. Quyết định và      │
│ kết quả            │         │ phản hồi               │
│                    │         │ 5–10 phút              │
│ Khách chờ xử lý    │         │ Nhân viên quyết định  │
│ 24–48 giờ; ví      │         │ bồi thường, soạn phản  │
│ thưởng tài xế có   │         │ hồi; Ops xử lý tiếp   │
│ thể bị phong tỏa   │         │ nếu cần                │
└────────────────────┘         └────────────────────────┘
```

- **🔴 Bottleneck:** Bước 2–3, mất **25–35 phút/ticket** để thu thập và đối soát dữ liệu đa nguồn. Tính cả bước ra quyết định và soạn phản hồi, thời gian thao tác ở các bước 2–4 vào khoảng **30–45 phút/ticket**; thời gian bước tiếp nhận chưa có số liệu.
- **🔄 Handoff:** Khách hàng → CSKH; CSKH chuyển dữ liệu giữa các hệ thống POD, cuộc gọi GSM và GPS; CSKH/Ops tiếp nhận nhận định để quyết định bồi thường và xử lý tài xế.
- **Thời gian hoàn tất hiện tại:** Theo card, **24–48 giờ** từ khiếu nại đến khi đóng vụ việc. Đây là thời gian hoàn tất, khác với 25–35 phút thao tác đối soát.
- **Tác động liên quan:** Khách hàng chờ bồi thường; tài xế có thể bị phong tỏa ví tiền thưởng trong nhiều ngày trong lúc xác minh. Cần đo tần suất và chi phí thực tế trước khi quy đổi thành tác động tài chính.

## 3.2. Problem Statement (6-field) & Metrics

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Nhân viên CSKH và Ops của Xanh SM xử lý ticket; khách hàng là người khiếu nại; tài xế là bên liên quan trong quá trình xác minh. |
| **2. Current Workflow** | CSKH nhận ticket về đơn giao hàng chưa nhận được, tự lấy ảnh xác nhận giao hàng (POD), log cuộc gọi GSM và tọa độ GPS qua 3–4 màn hình riêng biệt; sau đó đối chiếu ảnh, vị trí và thời gian dừng xe, rồi chuyển vụ việc sang bước quyết định bồi thường và soạn phản hồi. |
| **3. Bottleneck** | Thu thập và đối soát bằng chứng ở bước 2–3: **25–35 phút thao tác/ticket**. Dữ liệu phân tán, ảnh có thể không đủ bối cảnh, và việc xác minh thủ công làm thời gian đóng khiếu nại kéo dài. |
| **4. Business Impact** | Thời gian xử lý khiếu nại hiện được nêu là **24–48 giờ**; khách hàng phải chờ kết quả/bồi thường và tài xế có thể bị phong tỏa ví tiền thưởng nhiều ngày. Chưa có dữ liệu về số ticket, tỷ lệ khiếu nại đúng/sai hoặc chi phí bồi thường để ước tính tác động tổng thể. |
| **5. Success Metric** | 1. Giảm thời gian xác minh/đối soát từ **25 phút xuống dưới 1 phút/ticket**. 2. Giảm thời gian giải quyết từ **24–48 giờ xuống dưới 1 giờ**. 3. Hỗ trợ xử lý sơ bộ hợp lệ cho **trên 80% ticket**. 4. Trong pilot, theo dõi thêm tỷ lệ đề xuất được nhân viên chấp nhận và tỷ lệ kết luận sai; đặt ngưỡng an toàn sau khi đo baseline, không coi các mục tiêu tốc độ là bằng chứng về độ chính xác. |
| **6. Operational Boundary** | AI chỉ được phân tích dữ liệu được cấp quyền, tóm tắt bằng chứng, gắn cờ điểm bất thường/độ thiếu dữ liệu, tạo **Fraud Score tham khảo** và đề xuất bước xử lý. Nhân viên CSKH/Ops phải xem bằng chứng và duyệt mọi kết luận, phản hồi hoặc khoản bồi thường. AI **không được tự kết luận gian lận, từ chối bồi thường, phong tỏa/mở khóa ví tài xế, phát ngôn gửi khách hoặc thay đổi dữ liệu nguồn**. Thiếu dữ liệu, ảnh không rõ, kết quả mâu thuẫn hoặc độ tin cậy thấp thì chuyển kiểm tra thủ công. Lưu dấu vết dữ liệu đầu vào, kết quả AI và quyết định của người duyệt; chỉ dùng dữ liệu cần thiết theo quyền truy cập và chính sách lưu trữ được phê duyệt. |

## 3.3. Future-State Flow & AI Fit

- **AI Fit:** **LLM Feature** có khả năng xử lý ảnh và tổng hợp thông tin đa phương thức. Quy trình vẫn do con người điều khiển; không chọn Agentic Loop vì quyết định có thể ảnh hưởng trực tiếp đến bồi thường và thu nhập tài xế.
- **Phân chia trách nhiệm:** API/truy vấn có quyền truy cập lấy các trường dữ liệu xác định; LLM tóm tắt bằng chứng và tạo đề xuất có trích dẫn nguồn. Quy tắc nghiệp vụ xác định quyền truy cập, trạng thái ticket và điều kiện chuyển cấp; người có thẩm quyền ra quyết định cuối cùng.

```text
┌────────────────────┐   🔄    ┌────────────────────────┐   🔵    ┌─────────────────────────┐
│ 1. Nhận ticket     │ ──────→ │ 2. Thu thập dữ liệu    │ ──────→ │ 3. AI hỗ trợ đối soát   │
│ CSKH xác nhận mã   │         │ Có quyền truy cập:    │         │ Phân tích ảnh POD;      │
│ đơn và loại vấn đề │         │ ảnh POD, log cuộc gọi,│         │ tổng hợp GPS/thời gian; │
└────────────────────┘         │ GPS và metadata liên  │         │ nêu điểm khớp/lệch,    │
                               │ quan                 │         │ thiếu dữ liệu, nguồn   │
                               └────────────────────────┘         │ và Fraud Score tham khảo│
                                                                  └─────────────┬───────────┘
                                                                                │
                                                          đủ dữ liệu / tin cậy │ không đủ / mâu thuẫn /
                                                                                │ tin cậy thấp
                                                                                ▼
┌────────────────────────┐   🟢    ┌─────────────────────────┐       ↩️ Fallback:
│ 5. Nhân viên quyết định│ ←────── │ 4. CSKH/Ops review       │       Không tự kết luận;
│ duyệt/từ chối theo     │         │ kiểm tra nguồn, đối chiếu│       chuyển chuyên viên
│ quy trình hiện hành    │         │ bằng chứng và đề xuất    │       xác minh thủ công.
└────────────┬───────────┘         └─────────────────────────┘       Ticket tiếp tục theo
             │                                                           quy trình hiện tại.
             ▼
┌────────────────────────┐
│ 6. Gửi phản hồi, cập   │
│ nhật ticket và đóng   │
│ khiếu nại bởi nhân viên│
└────────────────────────┘
```

# 🏁 Phase 5 — EVALUATE (Nhóm, 20 min)

### Đánh giá readiness và quyết định

**AI Readiness Checklist**

1. [ ] Có sẵn dữ liệu mẫu/logs sạch, đã được phép sử dụng và có nhãn kết quả để đánh giá? — **Chưa có bằng chứng trong problem-scan; cần xác nhận với chủ dữ liệu.**
2. [x] Rủi ro khi AI sai có thể được kiểm soát bằng HITL và fallback? — **Có thể thiết kế kiểm soát này; cần kiểm thử trước khi pilot.**
3. [ ] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? — **Chưa xác nhận; cần thống nhất với CSKH, Ops và bộ phận quản lý tài xế.**

**Quyết định: [x] NOT YET — Cần xác lập baseline và chuẩn bị dữ liệu.**

**Justification:** Bài toán có bottleneck và mục tiêu định lượng rõ ràng: đối soát hiện tốn 25–35 phút, thời gian đóng khiếu nại là 24–48 giờ, còn mục tiêu là dưới 1 phút và dưới 1 giờ. Tuy nhiên, problem-scan chưa cung cấp bộ dữ liệu đã được phê duyệt, nhãn kết quả để đo độ chính xác, khả năng tích hợp an toàn với các nguồn POD/GSM/GPS, hay sự đồng thuận của các bên vận hành. Vì vậy, chưa đủ bằng chứng để quyết định triển khai production hoặc cam kết đạt các mục tiêu trên.

**Bước tiếp theo đề xuất:** xác minh quyền truy cập và chính sách dữ liệu; lấy mẫu ticket đã ẩn danh, có kết quả xử lý được nhân viên xác nhận; đo baseline theo từng công đoạn; sau đó chạy thử nghiệm offline để đo thời gian tiết kiệm, tỷ lệ đề xuất được chấp nhận và lỗi bỏ sót/phân loại sai. Chỉ xem xét pilot hỗ trợ nhân viên khi các chỉ số chất lượng và ngưỡng an toàn đã được chủ quy trình phê duyệt. Trong mọi giai đoạn, AI chỉ tư vấn; quyết định bồi thường và xử lý tài xế vẫn thuộc về người có thẩm quyền.
