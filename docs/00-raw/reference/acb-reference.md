# Báo cáo khảo sát trang tham chiếu ACB (acb-reference.md)

## 1. Thông tin khảo sát
- **Đối tượng khảo sát**: Công cụ tính toán lãi suất cho vay (Ngân hàng TMCP Á Châu - ACB).
- **Phương thức thu thập**: Khảo sát trực tiếp trên trình duyệt, kiểm thử các luồng nhập liệu, tương tác và hiển thị (do trang nạp bằng dynamic JS, không crawl tự động).
- **Mục tiêu**: Nắm rõ cấu trúc trường nhập, giới hạn, giá trị mặc định, phương thức tính và cách trình bày kết quả kế hoạch trả nợ của ngân hàng thương mại.

---

## 2. Danh sách trường nhập liệu (Input Fields)

| Tên trường | Kiểu dữ liệu | Đơn vị | Giá trị mặc định | Giới hạn (Min - Max) | Ghi chú hành vi |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Số tiền vay ($P$)** | Số nguyên | VND | 120.000.000 đ | 1.000.000 đ – 20.000.000.000 đ | Tự động định dạng dấu chấm phân cách hàng nghìn khi người dùng gõ. |
| **Thời hạn vay ($n$)** | Số nguyên | Tháng | 12 tháng | 1 – 360 tháng (tối đa 30 năm) | Có thể hỗ trợ chuyển đổi nhanh giữa Tháng và Năm. |
| **Lãi suất** | Số thập phân | % / năm | 10.5 %/năm | 0.0% – 35.0%/năm | Cho phép nhập 1-2 chữ số thập phân (ví dụ: 10.5%). |
| **Phương thức tính lãi** | Lựa chọn (Radio / Segment) | - | Dư nợ giảm dần | 1. Dư nợ giảm dần<br>2. Dư nợ ban đầu | Khi đổi phương thức, kết quả tính toán tự động cập nhật lại. |

---

## 3. Hiển thị kết quả (Outputs & Presentation)

### 3.1. Thẻ tóm tắt kết quả (Summary KPI Cards)
- **Số tiền phải trả kỳ đầu**: Hiển thị nổi bật vì đây là con số khách hàng vay quan tâm nhất để cân đối dòng tiền tháng đầu tiên.
- **Tổng số tiền lãi phải trả**: Tổng toàn bộ lãi trong suốt thời gian vay.
- **Tổng số tiền gốc và lãi**: Tổng số tiền thực tế người vay phải trả cho ngân hàng.

### 3.2. Bảng kế hoạch trả nợ chi tiết (Detailed Amortization Table)
- **Cấu trúc cột**:
  1. *Kỳ trả* (Tháng 1, 2, ..., $n$)
  2. *Dư nợ đầu kỳ*
  3. *Tiền gốc trả hàng kỳ*
  4. *Tiền lãi trả hàng kỳ*
  5. *Tổng số tiền phải trả trong kỳ* (Gốc + Lãi)
  6. *Dư nợ cuối kỳ* (còn lại)
- **Hành vi bảng**:
  - Hỗ trợ cuộn dọc mượt mà khi kỳ hạn dài (lên đến 120 - 360 tháng).
  - Có dòng tổng cộng ở cuối bảng hoặc đầu bảng.

---

## 4. Các trạng thái kiểm tra lỗi (Validation States)

1. **Trạng thái rỗng (Empty)**: Khi xóa trắng ô nhập, hiển thị thông báo lỗi yêu cầu nhập giá trị.
2. **Số tiền không hợp lệ**: Nhập số âm hoặc nhỏ hơn mức tối thiểu (ví dụ < 1.000.000 đ) $\rightarrow$ Báo lỗi đỏ dưới input.
3. **Kỳ hạn không hợp lệ**: Kỳ hạn $\le 0$ hoặc vượt quá 360 tháng $\rightarrow$ Báo lỗi vượt quá giới hạn cho phép.
4. **Lãi suất không hợp lệ**: Lãi suất âm hoặc vượt quá 50%/năm $\rightarrow$ Báo lỗi.

---

## 5. Dòng miễn trừ trách nhiệm (Legal Disclaimer)
- Trên trang ACB có dòng chú thích pháp lý rõ ràng:
  > *"Kết quả tính toán này chỉ mang tính chất tham khảo nhằm giúp Quý khách ước tính kế hoạch tài chính. Lãi suất và lịch trả nợ thực tế sẽ căn cứ theo hợp đồng tín dụng ký kết giữa Quý khách và Ngân hàng."*

---

## 6. Đánh giá rút ra cho dự án
- **Điểm học hỏi**:
  - Cách tổ chức bố cục trực quan: Cột trái là Form nhập liệu, Cột phải là Thẻ tóm tắt kết quả; Bảng lịch trả nợ đặt ở bên dưới.
  - Phân tách rõ ràng giữa 2 phương thức trả lãi (dư nợ giảm dần vs. dư nợ ban đầu).
- **Điểm cải tiến cho ứng dụng của chúng ta**:
  - **Bổ sung tính năng Xuất PDF chất lượng cao**: Trang ACB không hỗ trợ xuất báo cáo PDF A4 có layout chuẩn và hỗ trợ font tiếng Việt đẹp; chúng ta dùng `pdfmake` để sinh report A4 chuyên nghiệp.
  - **Tối ưu Mobile First**: Bảng kế hoạch trả nợ trên ACB khá khó nhìn trên điện thoại 360px; chúng ta sẽ tối ưu responsive dạng thẻ hoặc bảng cuộn ngang/sticky header thuận tiện.
  - **Tuyệt đối độc lập về thương hiệu**: Dùng design system riêng, không sử dụng logo, màu xanh hay bất kỳ tài sản nhận diện nào của ACB.
