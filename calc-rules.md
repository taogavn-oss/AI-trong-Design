# Quy tắc và Công thức Tính Lãi Vay (calc-rules.md)

Tài liệu định nghĩa các quy tắc và công thức toán học dùng để tính toán lãi vay, lập bảng kế hoạch trả nợ và tổng hợp kết quả.

---

## 1. Ký hiệu và Quy ước Tham số

- $P$: Số tiền vay ban đầu (Principal), đơn vị VND.
- $n$: Thời gian vay tính theo kỳ (thường là số tháng).
- $r$: Lãi suất mỗi kỳ (tháng) = $\frac{\text{Lãi suất năm}}{12}$ (dạng thập phân, ví dụ $12\%/\text{năm} \implies r = \frac{0.12}{12} = 0.01$).
- $k$: Kỳ trả nợ thứ $k$ ($k = 1, 2, \dots, n$).
- $B_0$: Dư nợ ban đầu, $B_0 = P$.
- $B_k$: Dư nợ còn lại sau kỳ trả thứ $k$.
- $B_{k-1}$: Dư nợ đầu kỳ $k$ (cuối kỳ $k-1$).
- $\text{gốc}_k$: Số tiền gốc phải trả trong kỳ $k$.
- $\text{lãi}_k$: Số tiền lãi phải trả trong kỳ $k$.
- $A$: Số tiền trả cố định mỗi kỳ (đối với phương thức Niên kim / Góp đều).

---

## 2. Các phương thức tính lãi

### 2.1. Dư nợ giảm dần, gốc đều
*Đặc điểm*: Gốc mỗi kỳ cố định, lãi tính trên dư nợ đầu kỳ thực tế còn lại.

$$
\text{gốc}_k = \frac{P}{n},\qquad \text{lãi}_k = B_{k-1}\cdot r,\qquad B_k = B_{k-1}-\text{gốc}_k,\qquad \sum \text{lãi} = P\,r\,\frac{n+1}{2}
$$

- **Tổng số tiền phải trả kỳ đầu**: $\text{gốc}_1 + \text{lãi}_1 = \frac{P}{n} + P \cdot r$
- **Tổng số tiền phải trả toàn bộ**: $P + \sum \text{lãi} = P \left(1 + r \frac{n+1}{2}\right)$

---

### 2.2. Dư nợ ban đầu
*Đặc điểm*: Gốc chia đều mỗi kỳ, tiền lãi mỗi kỳ luôn tính cố định dựa trên số tiền vay ban đầu.

$$
\text{gốc}_k = \frac{P}{n},\qquad \text{lãi}_k = P\cdot r,\qquad \sum \text{lãi} = P\,r\,n
$$

- **Tổng số tiền phải trả mỗi kỳ**: $\text{gốc}_k + \text{lãi}_k = \frac{P}{n} + P \cdot r$ (cố định mọi kỳ)
- **Tổng số tiền phải trả toàn bộ**: $P + P \cdot r \cdot n$

---

### 2.3. Góp đều (Niên kim / Annuity)
*Đặc điểm*: Tổng số tiền trả mỗi kỳ (gốc + lãi) là một hằng số cố định $A$. Lãi tính trên dư nợ đầu kỳ, phần còn lại của $A$ dùng để trả nợ gốc.

$$
A = \frac{P\,r}{1-(1+r)^{-n}},\qquad \text{lãi}_k = B_{k-1}\cdot r,\qquad \text{gốc}_k = A-\text{lãi}_k
$$

- **Dư nợ cuối kỳ**: $B_k = B_{k-1} - \text{gốc}_k$
- **Tổng số tiền phải trả toàn bộ**: $A \cdot n$
- **Tổng lãi**: $A \cdot n - P$

---

## 3. Test Vector kiểm thử minh họa (Benchmark)

**Ca kiểm thử A:**
- **Số tiền vay ($P$)**: 120.000.000 đ
- **Kỳ hạn ($n$)**: 12 tháng
- **Lãi suất năm**: 12%/năm $\implies r = 1\%/\text{tháng} = 0.01$

| Chỉ số | Dư nợ giảm dần (Gốc đều) | Dư nợ ban đầu |
| :--- | :--- | :--- |
| **Gốc mỗi kỳ ($\text{gốc}_k$)** | 10.000.000 đ | 10.000.000 đ |
| **Lãi kỳ 1 ($\text{lãi}_1$)** | 1.200.000 đ *(120.000.000 × 1%)* | 1.200.000 đ *(120.000.000 × 1%)* |
| **Lãi kỳ 2 / Kỳ 12** | 1.100.000 đ / 100.000 đ | 1.200.000 đ / 1.200.000 đ |
| **Phải trả kỳ 1** | 11.200.000 đ | 11.200.000 đ |
| **Tổng lãi ($\sum \text{lãi}$)** | 7.800.000 đ | 14.400.000 đ |
| **Tổng phải trả** | 127.800.000 đ | 134.400.000 đ |

---

## 4. Lưu ý về xử lý số liệu (Implementation Notes)

1. **Số nguyên VND**: Tất cả số tiền hiển thị và lưu trữ phải làm tròn thành số nguyên (`Math.round` tại từng bước trả hoặc điều chỉnh phần dư ở kỳ cuối cùng).
2. **Kỳ cuối cùng ($k = n$)**: Đảm bảo $B_n = 0$. Gốc kỳ cuối = $B_{n-1}$ để xử lý triệt để sai số làm tròn.
