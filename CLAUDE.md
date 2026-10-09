# Dự án: Loan Calculator (React + TypeScript + Vite + Tailwind)

Mô phỏng công cụ tính toán lãi vay ngân hàng và xuất báo cáo PDF (tham khảo nghiệp vụ công cụ tính lãi suất ACB, thiết kế giao diện độc lập).

## 1. Lệnh dự án
- **Dev**: `npm run dev`
- **Build**: `npm run build`
- **Test (Vitest)**: `npm run test`
- **Lint**: `npm run lint`
- **Typecheck**: `npm run typecheck`
- **E2E (Playwright)**: `npm run e2e`

## 2. Quy tắc làm việc cốt lõi
- **Chế độ làm việc**: Vào plan mode trước khi sửa nhiều file. Gặp điểm mơ hồ thì HỎI, không tự giả định nghiệp vụ.
- **Nguồn sự thật (Single Source of Truth)**:
  - Yêu cầu & Đặc tả: `specs/<feature>/spec.md` (kèm `client-request.md`, `Requirements.md`). Không làm việc ngoài phạm vi task.
  - Công thức & Quy tắc tính: [calc-rules.md](file:///d:/AI-trong-Design/calc-rules.md).
  - Quyết định kỹ thuật & nghiệp vụ: `docs/01-analysis/decision-log.md`.
- **Giới hạn & An toàn (Strict Guardrails)**:
  - KHÔNG: deploy, push trực tiếp nhánh `main`, đọc file `.env` hoặc API keys.
  - KHÔNG: thêm dependency mới khi chưa có phê duyệt từ Tech Lead.
  - KHÔNG: thu thập hoặc lưu trữ bất kỳ thông tin cá nhân nào của người dùng (Privacy by design, zero backend storage).

## 3. Nghiệp vụ tài chính & Xử lý số liệu
- **Nguyên tắc tiền VND**:
  - Tiền VND luôn là số nguyên (`integer`), tuyệt đối không cộng dồn tiền bằng số thực (`float`) để tránh lỗi tích lũy sai số thập phân.
  - Quy tắc làm tròn tuân thủ `calc-rules.md` / `decision-log.md`.
  - Kỳ cuối cùng ($k = n$) phải xử lý dứt điểm phần dư để dư nợ còn lại $B_n = 0$.
  - Định dạng hiển thị: Chuẩn tiền tệ Việt Nam (phân cách hàng nghìn bằng dấu chấm, kèm đơn vị `đ` hoặc `VND`, ví dụ: `120.000.000 đ`).
- **Kiến trúc Module tính toán**:
  - Tách logic tính toán thành module thuần (`pure functions`) tại `src/lib/loan/`, hoàn toàn độc lập với UI.
  - Bắt buộc vượt qua 100% các ca kiểm thử trong Test Vector chuẩn (Ca A: vay 120.000.000 đ, 12 tháng, 12%/năm) và các ca biên (kỳ hạn 1 tháng, kỳ hạn tối đa, lãi suất 0%).

## 4. Phạm vi tính năng (MoSCoW Scope)
- **Must (Bắt buộc cho MVP)**:
  - **Nhập liệu**: Số tiền vay ($P$), kỳ hạn vay ($n$ tháng), lãi suất (%/năm).
  - **Phương thức tính**: Chọn giữa 2 phương thức:
    1. *Dư nợ giảm dần* (gốc chia đều mỗi kỳ, lãi tính trên dư nợ thực tế đầu kỳ).
    2. *Dư nợ ban đầu* (gốc chia đều mỗi kỳ, lãi tính cố định trên số tiền vay ban đầu).
    *(Hỗ trợ sẵn sàng công thức Niên kim / Góp đều theo `calc-rules.md` khi mở rộng)*.
  - **Kiểm tra đầu vào (Validation)**: Kiểm tra giá trị rỗng, số âm, vượt giới hạn min/max; thông báo lỗi trực quan.
  - **Kết quả tổng hợp (KPI Cards)**: Tổng lãi phải trả, Tổng số tiền phải trả (Gốc + Lãi), Số tiền phải trả ở kỳ đầu tiên.
  - **Bảng kế hoạch trả nợ theo tháng (Amortization Table)**: Cột kỳ, dư nợ đầu kỳ, tiền gốc, tiền lãi, tổng trả trong kỳ, dư nợ cuối kỳ.
  - **Xuất file PDF**: Xuất báo cáo khổ A4 bằng `pdfmake` phía client; hỗ trợ bảng nhiều trang (lặp lại table header trên từng trang); nhúng font tiếng Việt có dấu đầy đủ; hiển thị dòng miễn trừ trách nhiệm.
  - **Responsive**: Tương thích hoàn hảo trên Mobile (360px), Tablet (768px), Desktop (1280px+).
  - **Dòng miễn trừ (Disclaimer)**: Luôn hiển thị dòng chữ: *“Kết quả mang tính tham khảo”*.
- **Should (Xem xét sau MVP)**: Ân hạn gốc, so sánh trực quan hai phương thức cạnh nhau, nút in trực tiếp.
- **Won't (Không thuộc phạm vi MVP)**: Đăng nhập, lưu dữ liệu người dùng vào backend, tính toán sổ tiết kiệm, tích hợp API ngân hàng/lãi suất realtime, đăng ký vay online.

## 5. Tiêu chuẩn Giao diện & Design System
- **Design Tokens**: Màu sắc, spacing, radius, font chỉ lấy từ `src/styles/tokens.css` và cấu hình theme Tailwind CSS tương ứng; cấm tuyệt đối hard-code mã màu hay khoảng cách tự do.
- **Component UI**: Ưu tiên tái sử dụng các component có sẵn trong `src/components/ui/` (Button, Input, Select, Card, Table, Alert...) trước khi viết mới.
- **Tiêu chuẩn tiếp cận (Accessibility)**: Tuân thủ WCAG 2.2 mức AA (tỷ lệ tương phản chữ/nền $\ge$ 4.5:1, hỗ trợ điều hướng phím, focus indicator rõ ràng, aria-label đầy đủ).
- **Thương hiệu**: Tham khảo chức năng nghiệp vụ của ACB, tuyệt đối không sao chép logo, hình ảnh, văn bản hay nhận diện thương hiệu của ACB.
