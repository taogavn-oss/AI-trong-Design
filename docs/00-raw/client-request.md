# Yêu cầu nguyên văn từ Client (Client Request)

## 1. Thông tin tiếp nhận yêu cầu
- **Dự án**: Công cụ tính lãi vay (Loan Calculator)
- **Người yêu cầu (Client)**: Đại diện Khách hàng / Đối tác tài chính
- **Người tiếp nhận**: BA / PO Dự án
- **Thời gian tiếp nhận**: 08/10/2026
- **Kênh tiếp nhận**: Email chính thức & Biên bản họp Kickoff
- **Link tham chiếu nghiệp vụ**: Công cụ tính toán lãi suất cho vay và tiết kiệm của Ngân hàng ACB (https://acb.com.vn/cong-cu-tinh-toan)

---

## 2. Nội dung yêu cầu nguyên văn (Verbatim Client Request)

> “Tạo màn hình công cụ tính lãi vay, mô phỏng lãi vay của ngân hàng và cho phép xuất file report pdf, tham khảo ở page sau: công cụ tính toán lãi suất cho vay và tiết kiệm của ACB”

> “Nhập số tiền vay, kỳ hạn, lãi suất %/năm; chọn phương thức tính (dư nợ giảm dần hoặc dư nợ ban đầu); kết quả tổng hợp (tổng lãi, tổng phải trả, số tiền phải trả kỳ đầu); bảng kế hoạch trả nợ theo tháng; xuất PDF; kiểm tra đầu vào; định dạng tiền VND; responsive; dòng miễn trừ “kết quả mang tính tham khảo””

---

## 3. Ghi chú và làm rõ ban đầu từ BA (Initial Observations)

1. **Khảo sát tham chiếu thủ công**:
   - Trang tham chiếu của ACB sử dụng kỹ thuật render phía client bằng JavaScript; không thể crawl tự động dữ liệu form. BA tiến hành mở trình duyệt, chụp màn hình các trạng thái (mặc định, nhập hợp lệ, kết quả, lỗi) và ghi chép chi tiết vào `docs/00-raw/reference/acb-reference.md`.
2. **Nguyên tắc tôn trọng bản quyền & Nhận diện thương hiệu**:
   - Chỉ tham khảo logic tính toán và trải nghiệm nghiệp vụ công cụ tính vay của ACB.
   - Tuyệt đối không sao chép logo, hình ảnh, văn bản quảng bá, bảng màu hay phong cách thương hiệu của ACB. Thiết kế theo design system độc lập của dự án.
3. **Phạm vi tính năng tập trung (Vay vs. Tiết kiệm)**:
   - Trên trang tham chiếu của ACB có cả công cụ tính tiền gửi tiết kiệm và tính vay.
   - Theo yêu cầu nguyên văn của client: Dự án chỉ tập trung vào **Công cụ tính lãi vay**. Tính toán tiết kiệm nằm ngoài phạm vi của MVP này.
