# Nhật ký Quyết định (decision-log.md)

Tài liệu ghi lại toàn bộ các quyết định nghiệp vụ, kiến trúc và phạm vi được thống nhất giữa Client, Product Owner (PO), Business Analyst (BA) và Tech Lead cho Dự án Loan Calculator.

---

## Bảng quyết định chính thức (Giai đoạn Phân tích yêu cầu - Cổng G1)

| ID | Ngày | Câu hỏi / Vấn đề (Q-xx) | Quyết định chính thức | Người quyết | Ảnh hưởng (FR/Task) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **D-001** | 08/10/2026 | **Q-01**: Hỗ trợ những phương thức tính lãi nào trong MVP? | Hỗ trợ 2 phương thức cốt lõi:<br>1. *Dư nợ giảm dần* (gốc đều).<br>2. *Dư nợ ban đầu*.<br>Phương thức Niên kim (Góp đều) đã có sẵn công thức tại `calc-rules.md`, xếp vào giai đoạn sau hoặc tùy chọn mở rộng. | Client, BA | FR-02, T-02, T-08 |
| **D-002** | 08/10/2026 | **Q-02**: Giới hạn và đơn vị của số tiền vay, kỳ hạn, lãi suất? | - Số tiền vay: 1.000.000 đ – 50.000.000.000 đ (mặc định 120.000.000 đ).<br>- Kỳ hạn: 1 – 360 tháng (mặc định 12 tháng).<br>- Lãi suất: 0.0% – 35.0%/năm (mặc định 10.5%/năm). | Client, BA | FR-01, FR-03, T-08 |
| **D-003** | 08/10/2026 | **Q-03**: Có hỗ trợ ân hạn gốc không? | MVP chưa hỗ trợ ân hạn nợ gốc. Chuyển tính năng này vào danh mục Should-have cho bản nâng cấp tiếp theo. | Client | FR-Scope, T-02 |
| **D-004** | 08/10/2026 | **Q-04**: Lãi suất cố định cả kỳ hay thả nổi/thay đổi theo giai đoạn? | Lãi suất tính cố định trong suốt kỳ hạn vay cho bản MVP để giữ giao diện và trải nghiệm đơn giản, dễ hiểu nhất cho người dùng. | Client | FR-01, T-02 |
| **D-005** | 08/10/2026 | **Q-05**: Quy tắc làm tròn tiền và quy ước tính ngày? | - Tính theo tháng chuẩn ($r = \text{Lãi suất năm} / 12$).<br>- Tiền VND luôn làm tròn số nguyên (`Math.round`) tại từng kỳ trả.<br>- Xử lý dứt điểm dư nợ ở kỳ cuối cùng: $\text{gốc}_n = B_{n-1}$ để đảm bảo dư nợ cuối cùng $B_n = 0$ tuyệt đối. | Client, BA, Tech Lead | FR-04, T-02 |
| **D-006** | 08/10/2026 | **Q-06**: Hiển thị lịch trả nợ theo ngày tháng thực hay theo kỳ 1, 2, 3? | Hiển thị theo "Kỳ 1, Kỳ 2, ... Kỳ n" (hoặc "Tháng 1, Tháng 2, ..."). Không bắt buộc nhập ngày giải ngân để tối ưu tốc độ thao tác người dùng. | Client, Designer | FR-05, T-09 |
| **D-007** | 08/10/2026 | **Q-07**: Nội dung và định dạng xuất PDF? | - Khổ giấy chuẩn A4 Portrait.<br>- Nội dung gồm: Tham số đầu vào, KPI tóm tắt, Toàn bộ bảng kế hoạch trả nợ (lặp header bảng ở mỗi trang), dòng miễn trừ trách nhiệm.<br>- Sử dụng font tiếng Việt đầy đủ dấu (Roboto/Inter) qua `pdfmake`.<br>- Không dùng logo hay nhãn hiệu ACB. | Client, Designer, Tech Lead | FR-06, T-10 |
| **D-008** | 08/10/2026 | **Q-08**: Thiết bị hỗ trợ và hình thức triển khai? | Xây dựng dưới dạng Single Page Application (SPA). Responsive tối ưu trên Mobile (360px), Tablet (768px), Desktop (1280px+). Hỗ trợ các trình duyệt hiện đại (Chrome, Safari, Firefox, Edge). | Client, Tech Lead | NFR-02, T-01, T-11 |
| **D-009** | 08/10/2026 | **Q-09**: Thu thập dữ liệu và bảo mật? | Không thu thập cookie theo dõi, không lưu dữ liệu người dùng, không backend. Mọi tính toán diễn ra 100% trên trình duyệt người dùng (Client-side privacy). | Client, Tech Lead | NFR-04, T-01 |
| **D-010** | 08/10/2026 | **Q-10**: Quy chuẩn định dạng số và ký hiệu tiền tệ? | - Định dạng số Việt Nam: Dấu chấm (.) phân cách hàng nghìn, dấu phẩy (,) phân cách phần thập phân.<br>- Ký hiệu tiền tệ: Sử dụng hậu tố `đ` (ví dụ: `120.000.000 đ`). | Client, Designer | FR-07, T-07 |

---

## Quy trình cập nhật Decision Log
- Mọi quyết định phát sinh sau khi đóng băng Spec (Cổng G2) bắt buộc phải thông qua quy trình **Change Request (CR)**.
- Người phê duyệt quyết định: PO và Tech Lead.
