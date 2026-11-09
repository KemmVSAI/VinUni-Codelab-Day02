🏗️ Phase 3 — DEEP-DIVE (Nhóm)
Bài toán: Ứng dụng AI hỗ trợ phát hiện và xác minh khiếu nại giao hàng không thành công thực tế (Delivery Fraud & Misdelivery Detection) trên Green SM Food.
Phạm vi: Tập trung vào trường hợp khách hàng phản ánh chưa nhận được đồ ăn dù ứng dụng đã cập nhật trạng thái đơn hàng là giao thành công.
Lưu ý về dữ liệu: Green SM Food là bối cảnh doanh nghiệp mục tiêu. Quy trình nội bộ, thời gian xử lý và các chỉ số dưới đây là giả định phục vụ thiết kế ban đầu, chưa phải dữ liệu vận hành đã được Green SM xác nhận. Nhóm cần kiểm chứng qua phỏng vấn hoặc khảo sát.

3.1. Current-State Workflow Mapping (25 min)
A. Mô tả quy trình hiện tại
Quy trình giả định khi khách hàng khiếu nại chưa nhận được đơn hàng:
Tài xế cập nhật trạng thái đơn hàng là đã giao thành công trên ứng dụng.
Khách hàng kiểm tra ứng dụng nhưng thực tế chưa nhận được đồ ăn.
Khách hàng liên hệ CSKH qua ứng dụng hoặc hotline để phản ánh.
Nhân viên CSKH tiếp nhận khiếu nại, tra cứu mã đơn hàng, trạng thái giao hàng, thông tin GPS, lịch sử liên hệ và bằng chứng giao hàng nếu có.
Nhân viên CSKH liên hệ tài xế, nhà hàng hoặc bộ phận vận hành để xác minh và đề xuất phương án xử lý.
B. Current-State Workflow Diagram
┌──────────────────────┐
│ BƯỚC 1               │
│ Tài xế cập nhật      │
│ đơn hàng đã giao     │
│                      │
│ Ai: Tài xế           │
│ ⏱ 1 phút (giả định)  │
│ In: Mã đơn, trạng thái│
│ Out: Đơn đã giao     │
└──────────┬───────────┘
           │ 🔄 Handoff:
           │ App → Khách hàng
           ▼
┌──────────────────────┐
│ BƯỚC 2               │
│ Khách kiểm tra đơn   │
│ nhưng chưa nhận món  │
│                      │
│ Ai: Khách hàng       │
│ ⏱ 2 phút (giả định)  │
│ In: Trạng thái đơn   │
│ Out: Phản ánh sự cố  │
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│ BƯỚC 3               │
│ Gửi khiếu nại đến    │
│ bộ phận CSKH         │
│                      │
│ Ai: Khách hàng       │
│ ⏱ 3 phút (giả định)  │
│ In: Nội dung khiếu nại│
│ Out: Ticket hỗ trợ   │
└──────────┬───────────┘
           │ 🔄 Handoff:
           │ Khách hàng → CSKH
           ▼
┌──────────────────────┐
│ BƯỚC 4 🔴            │
│ CSKH đối soát thông  │
│ tin và bằng chứng    │
│                      │
│ Ai: Nhân viên CSKH   │
│ ⏱ 8 phút (giả định)  │
│ In: Ticket, mã đơn,  │
│ GPS, log, ảnh nếu có │
│ Out: Hồ sơ xác minh  │
└──────────┬───────────┘
           │ 🔄 Handoff:
           │ CSKH → Tài xế/Ops
           ▼
┌──────────────────────┐
│ BƯỚC 5 🔴            │
│ Liên hệ các bên,     │
│ xác minh và đề xuất  │
│ hướng xử lý          │
│                      │
│ Ai: CSKH / Ops       │
│ ⏱ 6 phút (giả định)  │
│ In: Hồ sơ xác minh   │
│ Out: Kết quả đề xuất │
└──────────────────────┘

🔴 = Bottleneck
🔄 = Handoff

Tổng thời gian thao tác giả định: 20 phút/vụ việc.
Chưa tính thời gian chờ phản hồi từ các bên liên quan.
C. Phân tích Bottleneck
Bottleneck 1 – Bước 4: Đối soát thông tin
Nhân viên CSKH có thể phải mở nhiều hệ thống hoặc nguồn thông tin để kiểm tra:
Trạng thái và lịch sử cập nhật đơn hàng.
Vị trí GPS và hành trình tài xế.
Thời gian tài xế dừng tại địa điểm giao hàng.
Lịch sử cuộc gọi, tin nhắn giữa tài xế và khách hàng.
Ảnh hoặc bằng chứng giao hàng nếu hệ thống có lưu.
Vấn đề: Thông tin phân tán khiến nhân viên mất thời gian tổng hợp và khó phát hiện các dấu hiệu bất thường.
Bottleneck 2 – Bước 5: Xác minh và xử lý
Nhân viên phải liên hệ các bên liên quan để xác định nguyên nhân:
Tài xế đã giao đúng địa chỉ hay chưa?
Đơn hàng có bị giao nhầm người hoặc nhầm địa điểm không?
Có bằng chứng giao hàng hợp lệ không?
Có dấu hiệu bất thường trong hành trình giao hàng không?
Vấn đề: Việc xác minh phụ thuộc vào phản hồi của nhiều bên, khiến thời gian giải quyết có thể kéo dài.
Kết luận Current-State:
Vấn đề cốt lõi không chỉ là tiếp nhận khiếu nại mà là quá trình tổng hợp dữ liệu và xác minh trách nhiệm giữa khách hàng, tài xế, nhà hàng và bộ phận vận hành.

3.2. Problem Statement (6-field) & Metrics (15 min)
Field
Nội dung
1. Actor / Operator
Nhân viên chăm sóc khách hàng (CSKH) và nhân viên vận hành (Operations) của Green SM Food. Đây là những người trực tiếp tiếp nhận, đối soát và xử lý khiếu nại.
2. Current Workflow
Khi khách hàng phản ánh chưa nhận được đơn dù ứng dụng hiển thị đã giao thành công, CSKH tiếp nhận ticket, tra cứu mã đơn, trạng thái giao hàng, GPS, lịch sử liên hệ và bằng chứng giao hàng nếu có. Sau đó nhân viên liên hệ tài xế hoặc bộ phận vận hành để xác minh và đề xuất xử lý.
3. Bottleneck
Bước 4 và 5: Đối soát dữ liệu phân tán, kiểm tra bằng chứng và liên hệ các bên để xác minh. Thời gian thao tác giả định khoảng 14 phút/vụ việc, chưa tính thời gian chờ.
4. Business Impact
Khách hàng mất tiền hoặc không nhận được đồ ăn; CSKH tốn nguồn lực xử lý khiếu nại; tài xế có nguy cơ bị đánh giá sai nếu dữ liệu giao hàng không chính xác; doanh nghiệp có nguy cơ phát sinh chi phí bồi hoàn và suy giảm niềm tin của khách hàng. Chưa có số liệu thực tế xác nhận quy mô thiệt hại.
5. Success Metric
1. Giảm ít nhất 50% thời gian thao tác phân loại và đối soát ban đầu trong pilot. 2. Độ chính xác phân loại khiếu nại đạt ít nhất 90%. 3. Tỷ lệ cảnh báo sai không vượt quá 5% trên tập dữ liệu kiểm thử đã gán nhãn.
6. Operational Boundary
AI được phép đọc và phân loại khiếu nại, trích xuất thông tin đơn hàng, tổng hợp dữ liệu được cấp quyền và đề xuất mức độ rủi ro. AI tuyệt đối không được tự kết luận tài xế gian lận, tự khóa tài khoản, tự động quy trách nhiệm hoặc phê duyệt hoàn tiền. Nhân viên CSKH/Ops phải xác minh và quyết định cuối cùng.

Business Impact – Cách lượng hóa
Nhóm đề xuất thu thập các số liệu sau để chứng minh tác động kinh doanh:
Số lượng khiếu nại giao hàng không thành công mỗi ngày/tháng.
Thời gian thao tác trung bình của nhân viên cho một vụ việc.
Tỷ lệ khiếu nại được xác minh là giao sai, giao thiếu hoặc không có đủ bằng chứng giao hàng.
Chi phí bồi hoàn trung bình cho một khiếu nại hợp lệ.
Tỷ lệ khách hàng khiếu nại lại hoặc không hài lòng sau xử lý.
Có thể ước tính chi phí nhân sự xử lý khiếu nại theo công thức:
Chi phí xử lý/tháng = Số vụ việc × Thời gian thao tác trung bình × Chi phí nhân sự/giờ.
Các biến số cần được khảo sát hoặc lấy từ dữ liệu thử nghiệm, không tự trình bày như số liệu chính thức của doanh nghiệp.
Success Metrics – Bảng mục tiêu thử nghiệm
Metric
Baseline giả định
Target đề xuất
Thời gian thao tác phân loại và đối soát ban đầu
14 phút
≤ 7 phút
Độ chính xác phân loại khiếu nại
Chưa đo
≥ 90%
Tỷ lệ cảnh báo gian lận sai
Chưa đo
≤ 5%
Tỷ lệ hồ sơ cần nhân viên xử lý thủ công
Chưa đo
Theo dõi trong pilot

Lưu ý: Mục tiêu 5% cảnh báo sai chỉ được đánh giá trên tập dữ liệu có nhãn xác minh đáng tin cậy. Không sử dụng nó làm cam kết khi chưa có kết quả thử nghiệm.

3.3. Future-State Flow & AI Fit (25 min)
A. AI-Fit Matrix
Giải pháp
Vai trò
Đánh giá
Rule / State-Machine
Kiểm tra trạng thái đơn, thời gian cập nhật, điều kiện giao hàng và tính hợp lệ của dữ liệu
Cần thiết
LLM Feature
Đọc khiếu nại, phân loại sự cố, trích xuất thông tin và tổng hợp hồ sơ
Phù hợp cao
Agentic Loop
Tự lập kế hoạch và thực hiện nhiều hành động qua các công cụ
Chưa cần thiết

Quyết định công nghệ: Rule + LLM Feature.
Có thể bổ sung Machine Learning cho phát hiện bất thường khi có đủ dữ liệu lịch sử đã được xác minh và gán nhãn.
Lý do không chọn Agent:
Quy trình xử lý có các bước tương đối cố định.
Việc kết luận gian lận có rủi ro ảnh hưởng trực tiếp đến tài xế.
Chưa có nhu cầu để AI tự chủ ra quyết định hoặc thực hiện hành động nhạy cảm.
Rule, LLM và nhân viên phê duyệt đã đáp ứng được nhu cầu cốt lõi.
B. Future-State Workflow
┌───────────────────────────┐
│ BƯỚC 1                    │
│ Khách hàng gửi khiếu nại  │
│                           │
│ In: Mã đơn, nội dung      │
│ Out: Ticket khiếu nại     │
└─────────────┬─────────────┘
              ▼
┌───────────────────────────┐
│ BƯỚC 2 🔵 AI - LLM        │
│ Đọc và phân loại khiếu nại│
│                           │
│ - Chưa nhận được đơn      │
│ - Giao nhầm địa chỉ       │
│ - Thiếu bằng chứng giao   │
│ - Các sự cố khác          │
│                           │
│ Out: Nhãn và dữ liệu      │
│ đã trích xuất             │
└─────────────┬─────────────┘
              ▼
┌───────────────────────────┐
│ BƯỚC 3 ⚙️ RULE ENGINE     │
│ Kiểm tra dữ liệu đơn hàng │
│                           │
│ - Trạng thái đơn          │
│ - GPS và địa chỉ giao     │
│ - Thời gian giao          │
│ - Bằng chứng nếu có       │
│                           │
│ Out: Các dấu hiệu cần     │
│ xác minh                  │
└─────────────┬─────────────┘
              ▼
┌───────────────────────────┐
│ BƯỚC 4 🔵 AI - LLM        │
│ Tổng hợp hồ sơ khiếu nại  │
│ và đề xuất hướng xác minh │
│                           │
│ Out: Bản tóm tắt, mức độ  │
│ ưu tiên, đề xuất xử lý    │
└─────────────┬─────────────┘
              ▼
┌───────────────────────────┐
│ BƯỚC 5 🟢 HUMAN REVIEW    │
│ Nhân viên CSKH/Ops xem xét│
│                           │
│ - Kiểm tra bằng chứng    │
│ - Xác minh với các bên   │
│ - Quyết định xử lý       │
└─────────────┬─────────────┘
              ▼
       ┌───────────────┐
       │ Kết quả xử lý │
       │               │
       │ Hoàn tiền /   │
       │ giao bù /     │
       │ từ chối có    │
       │ căn cứ        │
       └───────────────┘

↩️ FALLBACK:
- Nếu LLM lỗi hoặc không đủ tự tin:
  Chuyển ticket cho nhân viên xử lý thủ công.
- Nếu thiếu GPS hoặc bằng chứng giao hàng:
  Đánh dấu "Thiếu dữ liệu xác minh".
- Nếu các nguồn dữ liệu mâu thuẫn:
  Không đưa ra kết luận tự động, chuyển CSKH/Ops kiểm tra.
C. Operational Boundary
AI được phép:
Đọc nội dung khiếu nại và phân loại loại sự cố.
Trích xuất mã đơn hàng, thời gian, địa điểm và thông tin liên quan.
Tổng hợp dữ liệu được cấp quyền truy cập.
Phát hiện các dấu hiệu bất thường và đề xuất mức độ ưu tiên.
Soạn bản tóm tắt và đề xuất phương án xử lý cho nhân viên.
AI tuyệt đối không được:
Tự kết luận tài xế có hành vi gian lận chỉ dựa trên GPS hoặc thời gian dừng.
Tự động khóa tài khoản hoặc xử phạt tài xế.
Tự động phê duyệt hoặc từ chối hoàn tiền.
Tự gửi thông báo mang tính kết luận buộc tội tài xế.
Sử dụng dữ liệu cá nhân ngoài phạm vi được cấp quyền.
Human-in-the-loop: Nhân viên CSKH/Ops là người xác minh bằng chứng và chịu trách nhiệm quyết định cuối cùng.

Kết luận Phase 3
Nhóm lựa chọn hướng triển khai Rule + LLM Feature, tập trung hỗ trợ nhân viên CSKH phát hiện dấu hiệu bất thường, tổng hợp bằng chứng và rút ngắn thời gian xác minh khiếu nại giao hàng.
Giải pháp không nhằm tự động kết luận tài xế gian lận mà nhằm nâng cao hiệu quả xử lý khiếu nại, đảm bảo tính công bằng cho khách hàng và tài xế, đồng thời giảm chi phí vận hành.
Các giả định cần xác minh trước khi triển khai thực tế:
Green SM Food có cung cấp quyền truy cập dữ liệu GPS và lịch sử đơn hàng cho bộ phận CSKH/Ops hay không.
Có lưu bằng chứng giao hàng và lịch sử liên hệ hay không.
Quy trình xử lý khiếu nại và chính sách bồi hoàn thực tế.
Baseline thời gian và số lượng khiếu nại thực tế.

---

# Phase 5 — EVALUATE

## 5.1. AI Readiness Checklist

| # | Câu hỏi đánh giá | Trạng thái hiện tại | Ý nghĩa với prototype |
|---:|---|---|---|
| 1 | Có sẵn dữ liệu mẫu/logs sạch để test không? | Chưa xác nhận | Prototype hiện dùng dữ liệu case mẫu do người kiểm thử cung cấp; chưa có logs vận hành thật. |
| 2 | Rủi ro khi AI sai có kiểm soát được qua HITL/Fallback không? | Có, ở mức thiết kế | AI chỉ tổng hợp bằng chứng và đề xuất bước xác minh; mọi quyết định hoàn tiền, phạt tài xế hoặc kết luận gian lận phải do nhân viên có thẩm quyền thực hiện. |
| 3 | Stakeholders có sẵn sàng thay đổi quy trình cũ không? | Cần xác nhận | CSKH/Ops có thể chấp nhận công cụ hỗ trợ, nhưng cần xác nhận với Green SM Food trước khi pilot. |

## 5.2. Quyết định cuối cùng

**Quyết định: GO — bắt đầu xây dựng Prototype với phạm vi hẹp.**

## 5.3. Justification

Việc chọn **GO** dựa trên ba căn cứ kỹ thuật:

1. **Problem-first:** bài toán bắt đầu từ một tình huống rõ ràng — app báo delivered nhưng khách hàng nói chưa nhận đồ ăn — thay vì tìm lý do để dùng AI.
2. **Phạm vi hẹp:** prototype chỉ làm phân loại sơ bộ, tổng hợp bằng chứng có sẵn, chỉ ra dữ liệu còn thiếu và đề xuất bước xác minh; không tự kết luận gian lận và không tự duyệt hoàn tiền.
3. **Kiến trúc hợp lý:** Rule + LLM Feature là lựa chọn phù hợp hơn Agentic Loop vì quy trình kiểm tra có cấu trúc và các hành động nhạy cảm cần human review.

Việc chọn **GO** không có nghĩa là triển khai production. Trong giai đoạn pilot cần xác minh:

- Green SM Food có cấp quyền truy cập GPS, lịch sử đơn hàng và bằng chứng giao hàng cho CSKH/Ops hay không.
- Có đủ dữ liệu mẫu đã gán nhãn để đo độ chính xác phân loại và tỷ lệ cảnh báo sai.
- Baseline thời gian xử lý khiếu nại thực tế.
- Quy trình phê duyệt hoàn tiền hoặc giao bù của doanh nghiệp.

Nếu các điều kiện trên chưa đáp ứng, trạng thái phù hợp hơn là **NOT YET** cho production, nhưng vẫn có thể tiếp tục **GO** cho prototype nội bộ và kiểm thử an toàn.
