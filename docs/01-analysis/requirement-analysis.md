# Bản Phân Tích Yêu Cầu (requirement-analysis.md)

- **Dự án**: Công cụ tính lãi vay ngân hàng (Loan Calculator)
- **Tác giả**: Business Analyst (BA) / Product Owner (PO)
- **Phiên bản**: v1.0
- **Ngày lập**: 08/10/2026
- **Trạng thái**: Đã thống nhất với Client (Sẵn sàng cho Cổng G1)
- **Tài liệu tham chiếu**:
  - Yêu cầu nguyên văn client: [client-request.md](file:///d:/AI-trong-Design/docs/00-raw/client-request.md)
  - Khảo sát ACB: [acb-reference.md](file:///d:/AI-trong-Design/docs/00-raw/reference/acb-reference.md)
  - Nhật ký quyết định: [decision-log.md](file:///d:/AI-trong-Design/docs/01-analysis/decision-log.md)
  - Quy tắc tính toán: [calc-rules.md](file:///d:/AI-trong-Design/calc-rules.md)

---

## 1. Mục tiêu sản phẩm (Project Objectives)

1. Xây dựng ứng dụng web hiện đại, dễ sử dụng, cho phép người dùng mô phỏng nhanh kế hoạch trả nợ vay ngân hàng.
2. Cung cấp kết quả tổng hợp tức thì (tiền trả kỳ đầu, tổng lãi, tổng số tiền phải trả) và bảng kế hoạch trả nợ chi tiết theo từng tháng.
3. Cho phép xuất file báo cáo PDF chuẩn khổ A4 chuyên nghiệp, hiển thị đúng font tiếng Việt có dấu để người vay hoặc nhân viên tư vấn tín dụng có thể lưu trữ và in ấn.
4. Đảm bảo tính toán chính xác tuyệt đối theo các phương thức tài chính phổ biến tại Việt Nam (Dư nợ giảm dần và Dư nợ ban đầu).

---

## 2. Đối tượng người dùng (User Personas)

1. **Khách hàng cá nhân có nhu cầu vay vốn (Borrowers)**:
   - Cần ước tính số tiền phải chi trả hàng tháng để cân đối ngân sách sinh hoạt trước khi quyết định vay mua nhà, mua xe, kinh doanh hoặc tiêu dùng.
   - Quan tâm lớn nhất: "Tháng đầu tiên tôi phải trả bao nhiêu tiền?" và "Tổng số tiền lãi cả kỳ là bao nhiêu?".
2. **Chuyên viên tư vấn tín dụng ngân hàng (Credit Officers)**:
   - Cần một công cụ trực quan, phản hồi tức thì để minh họa lịch trả nợ cho khách hàng trong các buổi tư vấn.
   - Cần xuất file báo cáo PDF nhanh để gửi qua Zalo/Email cho khách hàng.

---

## 3. Yêu cầu chức năng (Functional Requirements - MoSCoW)

### 3.1. Phân nhóm Must-have (Bắt buộc cho MVP)

| Mã ID | Tên chức năng | Mô tả chi tiết |
| :--- | :--- | :--- |
| **FR-01** | Nhập thông số khoản vay | Cho phép nhập 3 thông số: Số tiền vay ($P$, VND), Kỳ hạn vay ($n$, tháng), Lãi suất vay (%/năm). Hỗ trợ định dạng số tiền có dấu phân cách hàng nghìn. |
| **FR-02** | Chọn phương thức tính lãi | Cho phép người dùng chuyển đổi giữa 2 phương thức: <br>1. *Dư nợ giảm dần (Gốc đều)*: Gốc chia đều các kỳ, lãi tính trên dư nợ còn lại đầu kỳ.<br>2. *Dư nợ ban đầu*: Gốc chia đều, lãi tính cố định trên số tiền vay ban đầu. |
| **FR-03** | Kiểm tra đầu vào (Validation) | Xác thực dữ liệu ngay khi nhập: cảnh báo khi ô trống, số âm, hoặc ngoài biên ($P \in [1.000.000, 50.000.000.000]$, $n \in [1, 360]$, Lãi suất $\in [0.0, 35.0]$). |
| **FR-04** | Kết quả tổng hợp (KPI Cards) | Hiển thị nổi bật 3 chỉ số then chốt:<br>- Số tiền phải trả kỳ đầu tiên ($\text{gốc}_1 + \text{lãi}_1$).<br>- Tổng số tiền lãi phải trả ($\sum \text{lãi}$).<br>- Tổng số tiền phải trả (Gốc + Lãi). |
| **FR-05** | Bảng kế hoạch trả nợ | Hiển thị danh sách trả nợ chi tiết theo từng tháng gồm các cột: Kỳ, Dư nợ đầu kỳ, Tiền gốc, Tiền lãi, Tổng trả trong kỳ, Dư nợ cuối kỳ. |
| **FR-06** | Xuất báo cáo PDF | Tạo và tải xuống file PDF A4 chứa thông số vay, kết quả tóm tắt, toàn bộ bảng lịch trả nợ (lặp header bảng ở mỗi trang), hỗ trợ font tiếng Việt có dấu. |
| **FR-07** | Định dạng số tệ VND | Áp dụng dấu chấm (.) phân cách hàng nghìn, số nguyên không có số thập phân lẻ đối với tiền VND, hậu tố đơn vị `đ`. |
| **FR-08** | Dòng miễn trừ trách nhiệm | Hiển thị rõ ràng dòng lưu ý: *"Kết quả mang tính tham khảo"*. |

### 3.2. Phân nhóm Should-have (Kế hoạch giai đoạn 2)
- **FR-09**: Ân hạn trả nợ gốc (Grace period) trong $m$ tháng đầu.
- **FR-10**: Bảng so sánh trực quan hai phương thức tính cạnh nhau (Side-by-side comparison).
- **FR-11**: Nút in trực tiếp từ trình duyệt (Direct Print Styling).

### 3.3. Phân nhóm Could-have (Cân nhắc tương lai)
- **FR-12**: Biểu đồ trực quan cơ cấu dòng tiền trả nợ (tỷ trọng Gốc vs Lãi theo thời gian).
- **FR-13**: Hỗ trợ đa ngôn ngữ (Tiếng Việt / Tiếng Anh).

### 3.4. Phân nhóm Won't-have (Nằm ngoài phạm vi MVP)
- Đăng nhập, quản lý tài khoản người dùng.
- Lưu trữ lịch sử tính toán vào Database.
- Công cụ tính toán tiền gửi tiết kiệm.
- Tích hợp API ngân hàng / cập nhật lãi suất tự động.
- Tính năng đăng ký vay trực tuyến.

---

## 4. Yêu cầu phi chức năng (Non-Functional Requirements - NFR)

| Mã ID | Tiêu chí | Yêu cầu kỹ thuật |
| :--- | :--- | :--- |
| **NFR-01** | Độ chính xác tài chính | - Tiền VND luôn xử lý dưới dạng số nguyên (`integer`), không cộng dồn số thực.<br>- Làm tròn `Math.round` từng kỳ, kỳ cuối điều chỉnh gốc để dư nợ cuối cùng $B_n = 0$.<br>- Khớp 100% với Test Vector chuẩn (Ca A). |
| **NFR-02** | Khả năng đáp ứng (Responsive) | Giao diện hiển thị tối ưu trên 3 kích thước: Mobile (360px), Tablet (768px), Desktop (1280px+). |
| **NFR-03** | Khả năng tiếp cận (Accessibility) | Đạt chuẩn WCAG 2.2 AA: Tỷ lệ tương phản chữ/nền $\ge 4.5:1$, hỗ trợ bàn phím (Tab/Enter), hiển thị focus rõ nét. |
| **NFR-04** | Bảo mật & Riêng tư (Privacy) | Tính toán 100% tại Client (Browser). Không backend, không lưu dữ liệu, không gắn tracking/analytics xâm phạm quyền riêng tư. |
| **NFR-05** | Hiệu năng (Performance) | Thời gian cập nhật kết quả tính toán $< 50\text{ms}$ khi thay đổi thông số. Thời gian xuất PDF $< 1\text{s}$ cho khoản vay 360 tháng. |
| **NFR-06** | Tương thích trình duyệt | Hoạt động mượt mà trên Chrome, Firefox, Safari, Edge phiên bản mới nhất. |

---

## 5. Tiêu chí chấp nhận (Acceptance Criteria - Given/When/Then)

### Kịch bản 1: Nhập thông số và xem kết quả tính toán (Dư nợ giảm dần)
- **Given**: Người dùng đang ở màn hình công cụ tính lãi vay.
- **When**: Người dùng nhập:
  - Số tiền vay: `120.000.000` đ
  - Kỳ hạn: `12` tháng
  - Lãi suất: `12` %/năm
  - Chọn phương thức: "Dư nợ giảm dần"
- **Then**: Hệ thống tính toán và hiển thị:
  - Số tiền phải trả kỳ đầu: `11.200.000 đ`
  - Tổng số tiền lãi: `7.800.000 đ`
  - Tổng số tiền phải trả: `127.800.000 đ`
  - Bảng kế hoạch có 12 hàng, kỳ 1 trả 11.200.000 đ, kỳ 12 trả 10.100.000 đ, dư nợ cuối kỳ 12 bằng `0 đ`.

### Kịch bản 2: Đổi sang phương thức Dư nợ ban đầu
- **Given**: Người dùng giữ nguyên thông số: Vay `120.000.000` đ, `12` tháng, `12` %/năm.
- **When**: Người dùng chuyển phương thức sang "Dư nợ ban đầu".
- **Then**: Hệ thống ngay lập tức cập nhật:
  - Số tiền phải trả kỳ đầu: `11.200.000 đ`
  - Mỗi kỳ đều trả gốc `10.000.000 đ` và lãi `1.200.000 đ` (tổng trả mỗi kỳ `11.200.000 đ`).
  - Tổng tiền lãi: `14.400.000 đ`
  - Tổng tiền phải trả: `134.400.000 đ`.

### Kịch bản 3: Xử lý đầu vào không hợp lệ (Validation Error)
- **Given**: Người dùng đang ở form nhập liệu.
- **When**: Người dùng nhập số tiền vay `< 1.000.000` hoặc để trống ô số tiền.
- **Then**: Hệ thống hiển thị cảnh báo lỗi bằng màu đỏ ngay dưới ô nhập: "Số tiền vay tối thiểu là 1.000.000 đ", nút tính toán/kết quả tạm ẩn hoặc hiển thị trạng thái chưa hợp lệ.

### Kịch bản 4: Xuất báo cáo PDF
- **Given**: Người dùng đã có bảng kết quả tính toán hợp lệ.
- **When**: Người dùng nhấn nút "Xuất PDF".
- **Then**: Hệ thống tạo và tải xuống file PDF khổ A4 chứa đầy đủ thông tin khoản vay, tóm tắt kết quả, toàn bộ bảng lịch trả nợ, font chữ tiếng Việt có dấu hiển thị rõ ràng, chân trang có dòng chữ *"Kết quả mang tính tham khảo"*.

---

## 6. Giả định (Assumptions)

1. Khoản vay tính theo chu kỳ tháng đều (1 năm gồm 12 tháng, lãi suất tháng $r = \text{lãi suất năm} / 12$).
2. Lãi suất vay cố định trong toàn bộ thời gian vay trong phạm vi MVP.
3. Người dùng sử dụng các thiết bị có trình duyệt web hỗ trợ chuẩn HTML5 và chạy được JavaScript client-side.

---

## 7. Trạng thái các câu hỏi mở (Open Questions - Q-01 đến Q-10)

Toàn bộ 10 câu hỏi mở đã được giải quyết và chốt phương án chính thức cùng Client và Tech Lead trong [decision-log.md](file:///d:/AI-trong-Design/docs/01-analysis/decision-log.md):
- **Q-01**: Hỗ trợ 2 phương thức (Giảm dần & Ban đầu) $\rightarrow$ *Đã chốt (D-001)*.
- **Q-02**: Giới hạn: Tiền 1tr - 50 tỷ, Kỳ hạn 1 - 360 tháng, Lãi suất 0 - 35% $\rightarrow$ *Đã chốt (D-002)*.
- **Q-03**: Ân hạn gốc: Không có trong MVP $\rightarrow$ *Đã chốt (D-003)*.
- **Q-04**: Lãi suất cố định cả kỳ $\rightarrow$ *Đã chốt (D-004)*.
- **Q-05**: Làm tròn integer VND, dư nợ kỳ cuối về 0 $\rightarrow$ *Đã chốt (D-005)*.
- **Q-06**: Lịch hiển thị theo Kỳ 1, 2, ... n $\rightarrow$ *Đã chốt (D-006)*.
- **Q-07**: PDF A4 đầy đủ, lặp header, font tiếng Việt $\rightarrow$ *Đã chốt (D-007)*.
- **Q-08**: SPA độc lập, responsive 360/768/1280px $\rightarrow$ *Đã chốt (D-008)*.
- **Q-09**: Không analytics, không lưu trữ thông tin cá nhân $\rightarrow$ *Đã chốt (D-009)*.
- **Q-10**: Định dạng số Việt Nam (`120.000.000 đ`) $\rightarrow$ *Đã chốt (D-010)*.

---

## 8. Quản trị rủi ro (Risks & Mitigation Plan)

| Rủi ro | Mức độ | Biện pháp giảm thiểu |
| :--- | :--- | :--- |
| **Lỗi hiển thị font tiếng Việt trong PDF** | Cao | Sử dụng `pdfmake` tích hợp font TrueType (Inter hoặc Roboto) nhúng sẵn trong bộ nhớ ảo (VFS). |
| **Sai số làm tròn tích lũy khi tính tiền** | Cao | Tuân thủ nghiêm ngặt quy tắc tại `calc-rules.md`: Giữ tiền dạng integer, tính phần dư bù vào gốc kỳ cuối cùng. |
| **Bảng 360 hàng bị giật lag trên thiết bị di động yếu** | Trung bình | Tối ưu DOM bảng, sử dụng cuộn dọc với sticky header và thiết kế responsive thẻ tóm tắt. |
| **Sao chép nhầm lẫn nhận diện thương hiệu ACB** | Trung bình | Sử dụng bảng màu và Design System riêng biệt đã định nghĩa trong `tokens.css`. |

---

## 9. Phạm vi loại trừ (Out of Scope)

1. Công cụ tính lãi tiết kiệm (mặc dù trang tham chiếu ACB có).
2. Tích hợp thanh toán, đăng ký hồ sơ vay trực tuyến, kết nối CIC.
3. Đồng bộ với database ngân hàng hoặc cập nhật biểu lãi suất động từ API.

---

## 10. Checklist nghiệm thu Cổng G1 (PO/BA và Client Sign-off)

- [x] Mọi câu hỏi loại Blocker và Major (Q-01 đến Q-10) đã có quyết định chính thức trong `decision-log.md`.
- [x] Mỗi yêu cầu đều có ID rõ ràng (FR-xx, NFR-xx), phân loại MoSCoW và có tiêu chí chấp nhận Given/When/Then có thể kiểm thử được.
- [x] Có ít nhất một Test Vector chuẩn (Ca A: Vay 120.000.000 đ, 12 tháng, 12%/năm) để đối soát logic.
- [x] Giả định và phạm vi loại trừ được phân định rõ ràng.
- [x] Sẵn sàng chuyển giao sang **Pha 2: Đặc tả kỹ thuật (Spec v1.0)**.
