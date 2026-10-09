# Báo Cáo Phản Biện Độc Lập (phanbien.md)

- **Giai đoạn**: Pha 1 - Tiếp nhận và phân tích yêu cầu (Bước 1.4)
- **Vai trò**: Người phản biện độc lập (Independent Reviewer)
- **Tài liệu đối soát**:
  - [docs/01-analysis/requirement-analysis.md](file:///d:/AI-trong-Design/docs/01-analysis/requirement-analysis.md)
  - [docs/00-raw/client-request.md](file:///d:/AI-trong-Design/docs/00-raw/client-request.md)
  - [docs/00-raw/reference/acb-reference.md](file:///d:/AI-trong-Design/docs/00-raw/reference/acb-reference.md)
  - [calc-rules.md](file:///d:/AI-trong-Design/calc-rules.md)
- **Mục tiêu**: Phát hiện điểm mơ hồ, mâu thuẫn, thiếu edge case, yêu cầu không kiểm thử được và giả định chưa đánh dấu ảnh hưởng tới đúng/sai nghiệp vụ hoặc phạm vi sản phẩm.

---

## I. Mức độ BLOCKER (Ảnh hưởng trực tiếp đến tính đúng đắn nghiệp vụ tài chính)

### 1. Xung đột giữa Công thức lý thuyết và Tổng thực tế của Bảng lịch trả nợ ($\sum \text{lãi}$)
- **Hiện trạng**: Trong [calc-rules.md](file:///d:/AI-trong-Design/calc-rules.md), công thức tính tổng lãi của phương thức Dư nợ giảm dần là:
  $$\sum \text{lãi} = P \cdot r \cdot \frac{n+1}{2}$$
  Công thức đại số này chỉ đúng tuyệt đối trên trường số thực liên tục. Trong khi đó, tiêu chuẩn tài chính tại `NFR-01` và quyết định `D-005` bắt buộc làm tròn số nguyên VND (`Math.round`) tại **từng kỳ** trả nợ.
- **Vấn đề phát sinh**: Khi tính tổng cộng dồn các dòng thực tế đã làm tròn ($\sum_{k=1}^n \text{lãi}_k$), con số này có thể lệch từ 1 đến vài đồng so với công thức lý thuyết $P \cdot r \cdot \frac{n+1}{2}$. Nếu thẻ KPI "Tổng số tiền lãi" và báo cáo PDF hiển thị theo công thức lý thuyết, nó sẽ **không khớp** với tổng cột tiền lãi trong bảng kế hoạch trả nợ.
- **Hành động cần làm**: Quy định dứt khoát: Thẻ KPI "Tổng lãi" và "Tổng số tiền phải trả" bắt buộc phải được tính bằng **tổng cộng dồn các dòng trong bảng** ($\sum \text{lãi}_k$ và $\sum \text{gốc}_k + \sum \text{lãi}_k$), không lấy theo công thức đại số lý thuyết.

---

### 2. Thuật toán làm tròn Tiền gốc khi $P$ không chia hết cho $n$
- **Hiện trạng**: Đặc tả ghi công thức $\text{gốc}_k = \frac{P}{n}$ và ghi chú "gốc mỗi kỳ cố định". Nếu người dùng vay $10.000.000$ đ trong $3$ tháng, thì:
  $$\frac{P}{n} = \frac{10.000.000}{3} = 3.333.333,333\dots \text{ đ}$$
- **Vấn đề phát sinh**: 
  - Nếu làm tròn `Math.round(P / n)` cho các kỳ đầu: Kỳ 1 trả $3.333.333$ đ, Kỳ 2 trả $3.333.333$ đ $\implies$ Đã trả $6.666.666$ đ.
  - Kỳ 3 (kỳ cuối) phải gánh phần dư: $\text{gốc}_3 = 10.000.000 - 6.666.666 = 3.333.334$ đ.
  - Việc này mâu thuẫn với định nghĩa "gốc mỗi kỳ cố định". Nếu không quy định rõ thuật toán làm tròn ở $n-1$ kỳ đầu, các lập trình viên hoặc bộ test có thể cài đặt khác nhau (dùng `Math.floor`, `Math.ceil`, hoặc `Math.round`).
- **Hành động cần làm**: Chuẩn hóa thuật toán tính gốc:
  - Đối với các kỳ $k = 1, 2, \dots, n-1$: $\text{gốc}_k = \text{round}(P / n)$.
  - Đối với kỳ cuối cùng $k = n$: $\text{gốc}_n = B_{n-1}$ (toàn bộ dư nợ còn lại đầu kỳ cuối).

---

## II. Mức độ MAJOR (Ảnh hưởng đến phạm vi, hành vi và các ca biên quan trọng)

### 3. Mâu thuẫn giá trị giới hạn trần số tiền vay ($P_{\max}$)
- **Hiện trạng**:
  - Trong tài liệu khảo sát [acb-reference.md](file:///d:/AI-trong-Design/docs/00-raw/reference/acb-reference.md): Giới hạn trần số tiền vay ghi là **20.000.000.000 đ** (20 tỷ VND).
  - Trong [requirement-analysis.md](file:///d:/AI-trong-Design/docs/01-analysis/requirement-analysis.md) và `decision-log.md`: Giới hạn trần số tiền vay ghi là **50.000.000.000 đ** (50 tỷ VND).
- **Hành động cần làm**: BA và Client cần thống nhất một con số trần duy nhất để bộ phận Dev cấu hình validation và QA viết test case nhất quán (khuyến nghị chốt 20 tỷ theo ACB hoặc 50 tỷ cho khách hàng doanh nghiệp).

---

### 4. Thiếu đặc tả và tiêu chí chấp nhận cho 2 Edge Cases cốt lõi: $n = 1$ tháng và $r = 0\%$
- **Kỳ hạn $n = 1$ tháng**:
  - Kỳ 1 vừa là kỳ đầu tiên vừa là kỳ tất toán cuối cùng.
  - Cần quy định rõ: Bảng hiển thị duy nhất 1 hàng, số tiền trả kỳ đầu bằng chính tổng số tiền phải trả ($P + \text{lãi}_1$), dư nợ cuối kỳ bằng 0 đ ngay lập tức.
- **Lãi suất $r = 0\%$**:
  - Gói vay ưu đãi trả góp 0% hoặc người thân cho vay.
  - Tất cả tiền lãi các kỳ $\text{lãi}_k = 0$ đ, tổng lãi $\sum \text{lãi} = 0$ đ, tổng tiền phải trả bằng đúng số tiền vay ban đầu $P$.
- **Hành động cần làm**: Bổ sung 2 test vector chính thức cho 2 trường hợp biên này vào mục Tiêu chí chấp nhận (Acceptance Criteria).

---

### 5. Rủi ro tràn tài nguyên Client khi xuất PDF với kỳ hạn tối đa ($n = 360$ tháng)
- **Hiện trạng**: Kỳ hạn tối đa là 360 tháng (30 năm), sinh ra bảng dữ liệu 360 dòng, tương đương file PDF A4 dài 8 đến 10 trang.
- **Vấn đề phát sinh**: Việc render tài liệu PDF nhiều trang kèm nhúng font TrueType vào bộ nhớ ảo (VFS) bằng thư viện `pdfmake` hoàn toàn ở phía client có nguy cơ gây nghẽn main thread, đơ trình duyệt hoặc tràn bộ nhớ trên các thiết bị di động cấu hình yếu.
- **Hành động cần làm**: Yêu cầu kỹ thuật bổ sung: Cần có trạng thái Loading / Progress Indicator khi bấm nút xuất PDF và xử lý tạo file thông qua Web Worker hoặc xử lý bất đồng bộ tránh block giao diện người dùng.

---

### 6. Độ chính xác và bước nhảy của Lãi suất năm
- **Hiện trạng**: Tài liệu cho phép nhập lãi suất từ 0.0% đến 35.0%/năm.
- **Vấn đề phát sinh**: 
  - Chưa quy định số chữ số thập phân tối đa người dùng được nhập (ví dụ: `8.5%`, `8.55%` hay `8.555%`).
  - Lãi suất tháng $r = \frac{\text{Lãi suất năm}}{12}$. Nếu chia ra số vô hạn tuần hoàn (ví dụ: $10\% / 12 = 0.833333\dots\%$), hệ thống giữ nguyên số thực chính xác cao (`IEEE 754 float64`) hay làm tròn $r$? Nếu làm tròn $r$ trung gian trước khi nhân với $B_{k-1}$, sai số tích lũy qua 360 kỳ sẽ rất lớn.
- **Hành động cần làm**: Quy định cho phép nhập tối đa 2 chữ số thập phân cho lãi suất năm (bước nhảy `0.01%`). Lãi suất tháng $r$ tuyệt đối không làm tròn trung gian mà giữ nguyên trong biểu thức tính toán $\text{lãi}_k = \text{round}(B_{k-1} \cdot \frac{\text{lãi năm}}{12})$.

---

## III. Mức độ MINOR (Yêu cầu chưa đo lường được hoặc thiếu giả định minh thị)

### 7. Tiêu chí hiệu năng NFR-05 không thể nghiệm thu khách quan (Untestable Requirement)
- **Hiện trạng**: NFR-05 ghi "Thời gian cập nhật $< 50\text{ms}$" và "Thời gian xuất PDF $< 1\text{s}$" nhưng không gắn với cấu hình phần cứng chuẩn.
- **Hành động cần làm**: Bổ sung môi trường đo kiểm tham chiếu (ví dụ: Desktop Google Chrome tiêu chuẩn hoặc Mobile Chrome mô phỏng 4x CPU Throttling trong DevTools).

---

### 8. Thiếu giả định minh thị về các chi phí phát sinh ngoài lãi
- **Hiện trạng**: Trong thực tế vay ngân hàng luôn có: Phí bảo hiểm khoản vay, phí thẩm định tài sản, phí trả trước hạn. Khách hàng thực tế có thể thắc mắc nếu số tiền trả khác với hợp đồng tín dụng.
- **Hành động cần làm**: Bổ sung rõ ràng vào dòng miễn trừ và mục Giả định/Ngoài phạm vi: "Công cụ chỉ tính toán thuần túy nợ gốc và lãi vay danh nghĩa, chưa bao gồm các loại phí bảo hiểm, phí dịch vụ hoặc phụ phí tín dụng phát sinh".

---

### 9. Quy ước bước nhảy (Step) của ô nhập tiền tệ
- **Hiện trạng**: Ô nhập số tiền vay chưa định nghĩa rõ bước nhảy khi bấm mũi tên tăng/giảm hoặc phím Up/Down.
- **Hành động cần làm**: Quy định bước nhảy (step) là `1.000.000 đ` cho nút bấm nhanh, đồng thời cho phép người dùng gõ trực tiếp tự do đến hàng nghìn đồng (ví dụ: `15.500.000 đ`).

---

## Kết luận & Khuyến nghị tiếp theo
Tài liệu phân tích yêu cầu [requirement-analysis.md](file:///d:/AI-trong-Design/docs/01-analysis/requirement-analysis.md) đã có khung cấu trúc rất tốt. Để vượt qua Cổng G1 một cách an toàn và chuẩn mực:
1. BA cần cập nhật bổ sung các quyết định xử lý 2 mục **Blocker** và 4 mục **Major** vào [decision-log.md](file:///d:/AI-trong-Design/docs/01-analysis/decision-log.md).
2. Đồng bộ các điều chỉnh trên vào bản đặc tả [requirement-analysis.md](file:///d:/AI-trong-Design/docs/01-analysis/requirement-analysis.md) trước khi tiến hành đóng băng spec v1.0 ở Pha 2.
