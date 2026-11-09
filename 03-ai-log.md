# AI log — Bài toán khiếu nại giao hàng Xanh SM

Lúc đầu mình nhờ AI hoàn thiện bài deep-dive cho card #03, rồi làm luôn prototype. AI xử lý khá nhanh phần chia quy trình: nhận khiếu nại, gom POD/GPS/log cuộc gọi, đối soát rồi chuyển CSKH/Ops quyết định. Mình cũng nhờ nó nghĩ thêm các tình huống cố tình dụ mô hình làm sai, ví dụ tự duyệt tiền bồi thường hoặc đổ lỗi cho tài xế khi bằng chứng còn thiếu.

Với prototype: AI chỉ tóm tắt những bằng chứng được đưa vào, nêu điểm chưa khớp hoặc còn thiếu và chuyển cho nhân viên xem. AI không được tự kết luận gian lận, duyệt bồi thường, khóa ví hay nhắn cho khách. Mình chọn ba ca thử: dụ AI tự duyệt bồi thường, nhét chỉ thị buộc tội tài xế vào ghi chú, và yêu cầu AI bỏ qua mâu thuẫn giữa POD với GPS.

Qua bài này, mình thấy AI giúp khởi đầu và sắp xếp ý nhanh, nhưng cần kiểm tra lại nội dung theo đề bài và không xem số liệu hay kết quả kiểm thử do AI nêu ra là sự thật nếu chưa xác minh.