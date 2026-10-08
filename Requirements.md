 PHẦN 2. ÁP DỤNG: CÔNG CỤ TÍNH LÃI VAY CÓ XUẤT PDF
 Hướng dẫn từng bước từ yêu cầu của client đến bàn giao production, để tự thực hiện theo
B6. Use case và phạm vi
Yêu cầu của client (nguyên văn): “Tạo màn hình công cụ tính lãi vay, mô phỏng lãi vay của ngân hàng và cho phép xuất file report pdf, tham khảo ở page sau: công cụ tính toán lãi suất cho vay và tiết kiệm của ACB”  [52].
Ba điều phải làm rõ ngay từ đầu
•     Trang tham chiếu không đọc tự động được. Phần công cụ trên trang ACB nạp bằng JavaScript; khi thử đọc tự động để viết phụ lục này, các trường nhập và kết quả không xuất hiện trong dữ liệu trả về. BA phải tự mở trang, chụp màn hình và ghi lại; AI không được đoán.
•     Tham khảo chức năng, không sao chép. Không dùng logo, hình ảnh, văn bản hay bố cục của ACB; thiết kế riêng theo thương hiệu của client. Trang ACB còn có công cụ tiết kiệm, nhưng yêu cầu chỉ nói về vay nên tiết kiệm nằm ngoài phạm vi trừ khi client xác nhận.
•     Công thức dưới đây là giả định ban đầu, cần BA và client xác nhận. Hai cách tính phổ biến: dư nợ giảm dần (gốc chia đều, lãi tính trên dư nợ còn lại) và dư nợ ban đầu (lãi tính trên số tiền vay ban đầu); ngoài ra có thể có ân hạn gốc  [51].

 
Phạm vi MVP theo MoSCoW
Mức
Nội dung
Must
Nhập số tiền vay, kỳ hạn, lãi suất %/năm; chọn phương thức tính (dư nợ giảm dần hoặc dư nợ ban đầu); kết quả tổng hợp (tổng lãi, tổng phải trả, số tiền phải trả kỳ đầu); bảng kế hoạch trả nợ theo tháng; xuất PDF; kiểm tra đầu vào; định dạng tiền VND; responsive; dòng miễn trừ “kết quả mang tính tham khảo”.
Should
Ân hạn gốc; so sánh hai phương thức cạnh nhau; nút in.
Could
Biểu đồ cơ cấu gốc và lãi; song ngữ Việt/Anh.
Won't (MVP)
Đăng nhập, lưu dữ liệu người dùng, tính tiết kiệm, tích hợp API ngân hàng hay lãi suất realtime, đăng ký vay.


Công thức mẫu cho calc-rules.md - BA PM sẽ handle chuyện này (P là số tiền vay, n là số tháng, r là lãi suất tháng = lãi suất năm / 12; ba phương thức này là đề xuất, chốt ở câu hỏi 1 của G1):
Dư nợ giảm dần, gốc đều: gốc mỗi kỳ cố định, lãi tính trên dư nợ đầu kỳ.
\text{gốc}_k = \frac{P}{n},\qquad \text{lãi}_k = B_{k-1}\cdot r,\qquad B_k = B_{k-1}-\text{gốc}_k,\qquad \sum \text{lãi} = P\,r\,\frac{n+1}{2}
Dư nợ ban đầu: lãi mỗi kỳ tính trên số tiền vay ban đầu.
\text{gốc}_k = \frac{P}{n},\qquad \text{lãi}_k = P\cdot r,\qquad \sum \text{lãi} = P\,r\,n
Góp đều (niên kim): tổng trả mỗi kỳ cố định.
A = \frac{P\,r}{1-(1+r)^{-n}},\qquad \text{lãi}_k = B_{k-1}\cdot r,\qquad \text{gốc}_k = A-\text{lãi}_k
 

Test vector minh hoạ (do nhóm tự tính)
Ca A: vay 120.000.000 đ, 12 tháng, lãi suất 12%/năm. Hai ví dụ này tính tay được, dùng để minh hoạ cách viết test vector; chưa đối chiếu với kết quả của ACB hay client, BA phải kiểm lại trước khi đưa vào spec.
Chỉ số
Dư nợ giảm dần
Dư nợ ban đầu
Gốc mỗi kỳ
10.000.000 đ
10.000.000 đ
Lãi kỳ 1
1.200.000 đ (120.000.000 × 12% ÷ 12)
1.200.000 đ
Lãi kỳ 2 / kỳ 12
1.100.000 đ / 100.000 đ
1.200.000 đ / 1.200.000 đ
Phải trả kỳ 1
11.200.000 đ
11.200.000 đ
Tổng lãi
7.800.000 đ
14.400.000 đ
Tổng phải trả
127.800.000 đ
134.400.000 đ

 
Stack đề xuất
• 	React, TypeScript, Vite, Tailwind CSS: khớp đầu ra mặc định React và Tailwind của Figma MCP nên ít phải chuyển đổi  [36].
• 	pdfmake để xuất PDF phía client: hỗ trợ bảng nhiều trang và nhúng font TTF  [50]. Bắt buộc kiểm tra dấu tiếng Việt ở pha 5.
• 	Vitest và Playwright cho test; không backend, không lưu dữ liệu cá nhân, nên bề mặt rủi ro nhỏ; deploy tĩnh trên Vercel  [48]. Mọi dependency do Tech Lead duyệt (A4).
Thời lượng tham khảo
Giả định cho lần chạy đầu của nhóm 4 người (BA/PO, Designer, Dev, Tech Lead kiêm QA): P0 0,5 ngày (một lần); P1 0,5; P2 0,5; P3 0,5; P4 1,5 đến 2; P5 2 đến 3; P6 và P7 0,5 đến 1 mỗi pha (xen kẽ P5); P8 0,25; P9 0,25. Tổng khoảng 7 đến 9 ngày làm việc. Đây là giả định để lập kế hoạch, hãy đo lại thực tế trong pilot.
B7. Hướng dẫn thực hiện từng pha
Mỗi pha gồm bảng bước (ai làm gì), prompt mẫu và checklist cổng. Nguyên tắc chung: mỗi pha hoặc mỗi task mở một phiên mới; vào plan mode trước khi sửa nhiều file (Shift+Tab hai lần hoặc claude --permission-mode plan; Ctrl+G để sửa plan trực tiếp)  [31]; người duyệt plan rồi mới cho agent thực thi. Chỗ có <...> là giá trị bạn thay vào.
Pha 0. Chuẩn bị và kiểm soát (một lần cho mỗi dự án)
Vào: danh sách seat, quyền truy cập Figma, GitHub, Vercel. Ra: repo, rules và MCP đã duyệt. Cổng G0.
Bước
Ai
Việc cần làm
0.1
Program Owner, Tech Lead
Xác nhận điều kiện: thành viên đã ký cam kết sử dụng (mục 6); designer có Figma Full seat, dev có Dev seat trên gói Professional trở lên  [40]; repo GitHub private có main được bảo vệ và CODEOWNERS (A5). Ghi lại quyết định: hợp đồng với client có cho phép đưa tài liệu yêu cầu và file thiết kế vào công cụ AI không (nhóm dữ liệu “Ask first”).
0.2
Dev
Cài gh (đăng nhập) và uv. Cài Spec Kit: uv tool install specify-cli. Tạo dự án: specify init loan-calculator --integration claude, rồi cd loan-calculator. Nếu lệnh khác với tài liệu hiện hành, chạy specify --help và đối chiếu  [34] [35].
0.3
Program Owner
Cài MCP đã được duyệt: claude plugin install figma@claude-plugins-official và claude mcp add playwright -- npx @playwright/mcp@latest. Trong Claude Code gõ /mcp để xác thực Figma và kiểm tra trạng thái kết nối  [38] [46].
0.4
Dev, Tech Lead
Chạy /init để sinh CLAUDE.md rồi cắt gọn theo mẫu bên dưới; chạy /context kiểm tra file đã được nạp. Cấu hình .claude/settings.json: chặn đọc .env và key, allowlist lệnh test và lint (A5). Không dùng chế độ bỏ qua quyền  [21] [22].
0.5
Tech Lead
Thêm .github/pull_request_template.md (ô AI-assisted và một dòng “đã kiểm chứng bằng gì”) và CODEOWNERS cho CLAUDE.md, .claude/, .specify/, specs/, .mcp.json để thay đổi spec cần PO duyệt.
0.6
Designer
Tạo file Figma “Loan Calculator” với các trang: Cover, Foundations, Components, Wireframes, Screens, AI Sandbox. Quy ước: lưu version trước mỗi đợt AI ghi.

 
Mẫu CLAUDE.md (giữ ngắn; chỉ ghi điều agent không tự suy ra được)  [31]
# Dự án: Loan Calculator (React + TypeScript + Vite + Tailwind)
## Lệnh
- Test: npm run test | Lint: npm run lint | Type: npm run typecheck | E2E: npm run e2e
## Quy tắc làm việc
- Vào plan mode trước khi sửa nhiều file. Gặp điểm mơ hồ thì HỎI, không tự giả định
  nghiệp vụ.
- Nguồn sự thật: specs/<feature>/spec.md. Không làm việc ngoài phạm vi task.
- Tiền VND là số nguyên; không cộng dồn tiền bằng số thực; làm tròn theo decision-log.
- Màu, spacing, radius, font chỉ lấy từ src/styles/tokens.css; cấm hard-code.
- Ưu tiên component trong src/components/ui trước khi tạo mới.
- KHÔNG: deploy, push main, đọc .env, thêm dependency khi chưa hỏi.
Checklist G0 (Tech Lead):
[ ] 	Repo private, main được bảo vệ, CODEOWNERS và PR template đã có.
[ ] 	Danh sách MCP đã duyệt được ghi lại; không có MCP ngoài danh sách.
[ ] 	/context xác nhận CLAUDE.md đã nạp; không có secrets trong repo.
[ ] 	Quyết định về dữ liệu client và về chi phí Figma seat đã được ghi nhận.
 
Pha 1. Tiếp nhận và phân tích yêu cầu (khoảng 0,5 ngày)
Vào: yêu cầu nguyên văn của client, ảnh chụp trang tham chiếu. Ra: requirement-analysis.md, decision-log.md. Cổng G1.
Bước
Ai
Việc cần làm
1.1
BA/PO
Lưu yêu cầu nguyên văn vào docs/00-raw/client-request.md (ai nói, khi nào, kênh nào, link tham chiếu).
1.2
BA
Mở trang ACB  [52], chụp màn hình mọi trạng thái (mặc định, nhập hợp lệ, kết quả, lỗi) và ghi lại: danh sách trường, đơn vị, giới hạn, giá trị mặc định, cách hiển thị kết quả, các phương thức tính nếu có. Lưu vào docs/00-raw/reference/. Ảnh chỉ để hiểu chức năng, không đưa vào sản phẩm.
1.3
BA, AI
Mở phiên mới ở plan mode, chạy Prompt 1.3 để AI phỏng vấn bạn. Trả lời thật kỹ: đây là lúc rẻ nhất để sửa hiểu lầm  [31].
1.4
BA, AI
Mở phiên mới (context sạch), chạy Prompt 1.4 để AI đóng vai người phản biện độc lập  [32].
1.5
BA
Giải quyết các câu hỏi mở với client (bảng Q-01 đến Q-10 bên dưới). AI chỉ giúp soạn danh sách câu hỏi gửi client; quyết định do người ghi vào decision-log.md (ID, ngày, người quyết, nội dung).
1.6
BA
Cập nhật bản phân tích theo câu trả lời và gửi client xác nhận bằng văn bản.

 
Prompt 1.3 (phiên phỏng vấn, plan mode)
Mình là BA. Đọc @docs/00-raw/client-request.md và các ảnh trong
  @docs/00-raw/reference/.
Mục tiêu: phân tích yêu cầu cho công cụ tính lãi vay có xuất PDF.
Hãy phỏng vấn mình chi tiết bằng công cụ AskUserQuestion: nghiệp vụ tính lãi, đầu vào
  và giới hạn,
làm tròn, nội dung PDF, thiết bị, trường hợp biên, rủi ro. Đừng hỏi điều hiển nhiên.
Quy tắc: KHÔNG tự giả định nghiệp vụ. Điều chưa rõ ghi vào mục Câu hỏi mở, kèm người cần trả lời.
Khi đủ thông tin, viết docs/01-analysis/requirement-analysis.md gồm: mục tiêu, người
  dùng, yêu cầu chức năng
(ID FR-xx, MoSCoW), phi chức năng (NFR-xx), tiêu chí chấp nhận dạng Given/When/Then, giả định, câu hỏi mở (Q-xx), rủi ro, ngoài phạm vi.
 
Prompt 1.4 (phiên phản biện, context mới)
Bạn là người phản biện độc lập. Đọc @docs/01-analysis/requirement-analysis.md và
  @docs/00-raw/.
Tìm: điểm mơ hồ, mâu thuẫn, thiếu trường hợp edge case, yêu cầu không kiểm thử được, giả định chưa được đánh dấu.
Chỉ báo những phát hiện ảnh hưởng tới đúng/sai nghiệp vụ hoặc phạm vi; bỏ qua phong cách.
Xuất danh sách đánh số theo mức độ (Blocker/Major/Minor). Không sửa file.
Các câu hỏi mở dự kiến cho use case này
ID
Câu hỏi cần chốt
Ai trả lời
Q-01
Hỗ trợ những phương thức nào: dư nợ giảm dần, dư nợ ban đầu, niên kim (trả đều mỗi kỳ)?
Client, BA
Q-02
Giới hạn và đơn vị của số tiền vay, kỳ hạn (tháng hay năm), lãi suất (%/năm)?
Client
Q-03
Có ân hạn gốc không? Nếu có, tối đa bao nhiêu tháng?
Client
Q-04
Lãi suất cố định cả kỳ hay thay đổi theo giai đoạn (ưu đãi N tháng đầu)?
Client
Q-05
Quy tắc làm tròn: làm tròn từng kỳ hay cuối cùng; quy ước số ngày (30/360 hay ngày thực tế)?
Client, BA
Q-06
Có hiển thị lịch trả theo ngày tháng thực (cần ngày giải ngân) hay chỉ theo kỳ 1, 2, 3?
Client
Q-07
Nội dung PDF: tham số, tóm tắt, bảng đầy đủ mọi kỳ, logo, thông tin liên hệ, dòng miễn trừ; ngôn ngữ?
Client, Designer
Q-08
Thiết bị và trình duyệt cần hỗ trợ; nhúng vào website của client hay trang độc lập?
Client, Tech Lead
Q-09
Có thu thập analytics hoặc lưu dữ liệu người dùng không (mặc định: không)?
Client, Tech Lead
Q-10
Định dạng số: dấu chấm phân cách hàng nghìn, dấu phẩy thập phân; ký hiệu tiền tệ (đ hay ₫)?
Client, Designer

 
Checklist G1 (PO/BA và client):
[ ] 	Mọi câu hỏi Blocker và Major đã có câu trả lời trong decision-log.
[ ] 	Mỗi yêu cầu có ID, mức MoSCoW và tiêu chí chấp nhận kiểm thử được.
[ ] 	Giả định và phạm vi loại trừ được liệt kê rõ; client xác nhận bằng văn bản.
 
Pha 2. Spec là nguồn sự thật (khoảng 0,5 ngày)
Vào: requirement-analysis, decision-log. Ra: constitution và spec v1.0 đóng băng. Cổng G2. Cách gọi lệnh Spec Kit có thể khác theo agent và phiên bản; gõ /speckit để xem dạng đang hiển thị  [35].
Bước
Ai
Việc cần làm
2.1
Tech Lead, AI
Chạy /speckit-constitution với Prompt 2.1. Con người đọc kỹ và sửa: constitution ràng buộc mọi bước sau.
2.2
BA, AI
Chạy /speckit-specify với Prompt 2.2: spec chỉ nói cái gì và vì sao, không nêu công nghệ  [34].
2.3
BA, AI
Chạy /speckit-clarify; BA trả lời các câu hỏi AI nêu ra và cập nhật spec.
2.4
BA
Thêm mục “Ví dụ chấp nhận” vào spec: các test vector tính độc lập bằng Excel (xem Ca A ở B6), thêm ca biên (số tiền nhỏ nhất, kỳ hạn 1 tháng, kỳ hạn tối đa, lãi suất 0%).
2.5
AI (phiên mới)
Chạy /speckit-checklist và /speckit-analyze để kiểm tra chất lượng yêu cầu và độ nhất quán giữa các tài liệu; Tech Lead đọc kết quả  [34].
2.6
PO, Tech Lead, Designer
Mở PR “spec v1.0”, cả ba người duyệt (CODEOWNERS), merge, gắn tag spec-v1.0. Từ đây mọi thay đổi đi qua Change Request (mẫu ở B8).

 
Prompt 2.1
/speckit-constitution Nguyên tắc dự án: (1) mọi logic tính tiền có test dựa trên test
  vector do BA cung cấp;
(2) tiền VND là số nguyên, quy tắc làm tròn lấy từ decision-log; (3) tuân thủ WCAG 2.2
  mức AA;
(4) không thu thập hoặc lưu dữ liệu cá nhân; (5) giao diện chỉ dùng design token và
  component của design system;
(6) mọi thay đổi spec sau khi đóng băng phải qua Change Request.
 
Prompt 2.2
/speckit-specify Dựa trên @docs/01-analysis/requirement-analysis.md và
  @docs/01-analysis/decision-log.md,
viết spec cho công cụ tính lãi vay có xuất PDF. Chỉ mô tả CÁI GÌ và VÌ SAO: người
  dùng, kịch bản, yêu cầu chức năng
(giữ nguyên ID FR/NFR), tiêu chí chấp nhận, ngoài phạm vi. Không nêu công nghệ. Mọi
  chỗ chưa rõ ghi thành câu hỏi, không tự suy đoán.
Checklist G2 (PO, Tech Lead, Designer):
[ ] 	Mỗi FR có tiêu chí chấp nhận kiểm thử được và ít nhất một test vector.
[ ] 	Spec không chứa quyết định công nghệ; không còn câu hỏi mở loại Blocker.
[ ] 	Đã merge, gắn tag spec-v1.0; Change Request là cách duy nhất để sửa tiếp.
 
Pha 3. Phân chia công việc theo bộ phận (khoảng 0,5 ngày)
Vào: spec v1.0. Ra: plan, các brief theo vai trò, tasks, GitHub Issues. Cổng G3.
Bước
Ai
Việc cần làm
3.1
Tech Lead, AI
Chốt công nghệ (quyết định của con người), rồi chạy /speckit-plan với Prompt 3.1. Đọc plan ở plan mode; Ctrl+G để sửa trực tiếp  [31]. Danh sách dependency đề xuất phải được Tech Lead duyệt.
3.2
PM/Tech Lead, AI
Chạy Prompt 3.2 để sinh ba brief trong docs/03-handoff/. Mỗi chủ sở hữu vai trò (Designer, Dev, QA) duyệt brief của mình.
3.3
Tech Lead, AI
Chạy /speckit-tasks; yêu cầu gắn nhãn vai trò, phụ thuộc, tiêu chí chấp nhận và lệnh kiểm chứng cho từng task. Đối chiếu với bảng task mẫu bên dưới.
3.4
Tech Lead
Duyệt danh sách task. Sau đó cho agent tạo issue bằng gh issue create (duyệt từng lệnh hoặc theo lô, vì là thao tác mạng); tạo Project board: Backlog, Ready, In progress, In review, Done.

 
Prompt 3.1
/speckit-plan Dùng React + TypeScript + Vite + Tailwind CSS. Logic tính toán là module
  thuần trong src/lib/loan,
không phụ thuộc UI. Xuất PDF phía client bằng pdfmake. Test bằng Vitest và Playwright.
  Không backend.
Liệt kê mọi dependency mới để Tech Lead duyệt trước khi cài.
 
Prompt 3.2
Từ @specs/<feature>/spec.md và plan.md, tạo 3 tài liệu trong docs/03-handoff/:
1) design-brief.md: danh sách màn hình và trạng thái (rỗng, hợp lệ, lỗi, kết quả;
  mobile và desktop), nội dung chữ tiếng Việt mẫu,
   yêu cầu design system (token, component, state), a11y, khung PDF A4, tiêu chí Ready
 	for dev.
2) dev-brief.md: cấu trúc thư mục, ranh giới module, quy ước, thứ tự làm, cách kiểm
  chứng từng phần.
3) qa-plan.md: ma trận test (FR -> test), test vector, thiết bị và trình duyệt, tiêu
  chí nghiệm thu.
Mỗi tài liệu phải trích dẫn ID FR/NFR. Không thêm yêu cầu mới.

Bảng task mẫu cho use case
Task
Vai trò
Nội dung
Phụ thuộc
Kiểm chứng
T-01
Dev
Khởi tạo Vite, TypeScript, Tailwind, lint, test, CI
Không
lint và test chạy xanh
T-02
Dev
Module tính lãi (hai phương thức) theo test vector
T-01
Test vector và các bất biến đạt
T-03
Designer
Wireframe thấp độ chính xác S1-S4
Spec
G4a
T-04
Designer
Design system: foundations và components
T-03
G4b
T-05
Designer
Hi-fi S1-S4 và frame PDF A4; Ready for dev
T-04
G4b
T-06
Dev
Token từ Figma sang tokens.css và theme Tailwind
T-04
Khớp get_variable_defs
T-07
Dev
Component UI nền (Button, Input, Select, Card, Table, Alert)
T-05, T-06
So ảnh chụp với Figma
T-08
Dev
Màn hình tính toán: form, kiểm tra đầu vào, kết quả
T-02, T-07
E2E luồng chính
T-09
Dev
Bảng kế hoạch trả nợ
T-08
Đúng số liệu, cuộn mượt
T-10
Dev
Xuất PDF (font tiếng Việt, bảng nhiều trang)
T-02, T-05
PDF mở được, số liệu khớp màn hình
T-11
QA
E2E, a11y, responsive
T-08 đến T-10
Báo cáo test
T-12
Tech Lead
Preview, staging, checklist phát hành
T-11
G8
T-13
BA/PO
UAT với client
T-12
Biên bản UAT
T-14
Cả nhóm
Handoff pack và retro
T-13
G9

 
Checklist G3 (Tech Lead và PO):
[ ] 	Mọi FR/NFR có ít nhất một task; mọi task có vai trò, phụ thuộc, tiêu chí chấp nhận.
[ ] 	Thứ tự hợp lý: task thiết kế đứng trước task giao diện; logic tính toán chạy song song với thiết kế.
[ ] 	Dependency mới đã được duyệt; issue đã tạo và gán người.
 
Pha 4. Design system và wireframe trên Figma (khoảng 1,5 đến 2 ngày)
Vào: spec, design-brief. Ra: file Figma có wireframe, design system và hi-fi, đã gắn Ready for dev. Cổng G4a (wireframe) và G4b (design system và hi-fi).
Bước
Ai
Việc cần làm
4.1
Designer
Lưu version Figma (“Before AI: wireframe”). Mở phiên Claude Code mới, kiểm tra /mcp đã kết nối Figma. Chỉ cho AI ghi trên trang AI Sandbox và chỉ khi bạn ra lệnh  [37].
4.2
Designer, AI
Dựng wireframe bằng Prompt 4.2. Mỗi màn một lệnh nhỏ, dừng để duyệt, vì mỗi lần ghi tối đa 20kb  [37].
4.3
Designer, PO, Dev
Duyệt wireframe: sửa tay chi tiết nhỏ; thay đổi lớn thì ra lệnh bằng prompt ngắn (ví dụ “trên mobile, đưa bảng kết quả xuống dưới form”). Dev xác nhận khả thi. Đạt G4a trước khi làm tiếp.
4.4
Designer, AI
Tạo foundations bằng Prompt 4.4, chạy riêng để duyệt từng nhóm token.
4.5
Designer
Duyệt foundations: kiểm tra tương phản, tên token, chỉnh màu thương hiệu bằng tay.
4.6
Designer, AI
Tạo components bằng Prompt 4.6, mỗi đợt 3 đến 4 component, duyệt rồi mới tiếp.
4.7
Designer, AI
Dựng hi-fi và frame PDF A4 bằng Prompt 4.7; AI phải dùng component và variable đã có thay vì tạo mới  [36].
4.8
Designer, AI
Chạy Prompt 4.8 để kiểm tra chất lượng (chỉ báo cáo). Designer đọc báo cáo, quyết định sửa gì, rồi mới cho AI sửa phần được duyệt.
4.9
Designer
Hoàn thiện: thêm annotations cho hành vi (kiểm tra đầu vào, định dạng số, trạng thái lỗi); publish component lên team library (cần cho Code Connect  [37]); đặt trạng thái Ready for dev cho các frame đã xong  [44].
4.10
Designer, PO, Tech Lead
Duyệt G4b theo checklist.

 
Prompt 4.2 (wireframe)
/figma-generate-design Trong file <FIGMA_FILE_URL>, trang AI Sandbox: tạo wireframe
  thấp độ chính xác (xám, không màu thương hiệu)
theo @docs/03-handoff/design-brief.md: S1 form nhập (mặc định), S2 form có lỗi kiểm
  tra đầu vào,
S3 kết quả tổng hợp và bảng kế hoạch trả nợ, S4 bản mobile 360px. Dùng auto layout,
  tên layer có ngữ nghĩa.
Mỗi màn một frame. Làm từng màn một và dừng để mình duyệt. Chưa tạo design system.
  Không tạo trang hay file khác.
 
Prompt 4.4 (foundations)
/figma-generate-library Trong file <FIGMA_FILE_URL>, tạo foundations dưới dạng Figma
  variables (mode Light):
- Color: primitive (neutral, brand, success, warning, error) và semantic
  (text/primary, text/secondary, surface/default,
  surface/muted, border/default, action/primary...). Cặp chữ và nền đạt WCAG 2.2 AA
    (>= 4.5:1 cho chữ thường).
- Typography: font Inter (có glyph tiếng Việt), thang cỡ chữ 12 đến 32, line-height.
- Spacing theo bước 4px, radius, elevation, breakpoint 360/768/1280.
Đặt tên dạng group/name. Báo cáo danh sách variable đã tạo. Chưa tạo component.
 
Prompt 4.6 (components)
/figma-generate-library Dùng variables đã có (không hard-code giá trị), tạo component
  set: Button (primary/secondary/ghost;
default/hover/focus/disabled/loading; size M/L), TextInput và CurrencyInput
  (default/focus/filled/error/disabled + helper text),
SegmentedControl (chọn phương thức), Select, Card, StatCard (KPI), Table (header, row,
  zebra, sticky header),
Alert/Disclaimer, Tooltip, Toast. Mỗi component có variant properties, auto layout, mô
  tả ngắn.
Làm 3 đến 4 component mỗi lần và dừng để mình duyệt.
 
Prompt 4.7 (hi-fi và PDF A4)
/figma-generate-design Dùng đúng component và variable trong thư viện của file này
  (tìm trong design system trước khi tạo mới).
Dựng hi-fi S1-S4 từ wireframe đã duyệt, và frame PDF Report A4 (595x842) gồm: tiêu đề,
  tham số khoản vay,
tóm tắt kết quả, bảng kế hoạch trả nợ (header lặp lại mỗi trang), dòng miễn trừ. Dùng
  dữ liệu mẫu tiếng Việt có dấu
và số tiền dạng 120.000.000 đ. Không hard-code màu hay spacing.
 
Prompt 4.8 (kiểm tra chất lượng)
/figma-use Kiểm tra trang Screens trong <FIGMA_FILE_URL>. Liệt kê:
  fill/stroke/text/spacing chưa gắn variable;
layer còn tên mặc định (Frame 123, Group 5); component thiếu state; frame không dùng
  auto layout.
Chỉ báo cáo, CHƯA sửa.
Checklist G4a (wireframe): đủ màn hình và trạng thái theo spec; luồng người dùng đúng; PO duyệt; Dev xác nhận khả thi.
Checklist G4b (design system và hi-fi):
[ ] 	100% fill, stroke, text và spacing dùng variable; không còn layer tên mặc định.
[ ] 	Component có đủ variant và state; auto layout; thu phóng frame không vỡ bố cục.
[ ] 	Tương phản đạt WCAG 2.2 AA  [53]; text tiếng Việt có dấu hiển thị đúng; frame PDF A4 đã duyệt.
[ ] 	Annotations đủ; component đã publish; frame đã đặt Ready for dev; PO và Tech Lead đã duyệt.
 
Pha 5. Triển khai code (khoảng 2 đến 3 ngày)
Vào: các frame Ready for dev, dev-brief, tasks. Ra: PR nhỏ đã review, merge vào nhánh tích hợp. Cổng G5 (mỗi PR). Mỗi task: nhánh riêng, phiên mới, plan mode trước.
Bước
Ai
Việc cần làm
5.1
Dev, Tech Lead
T-01: để agent dựng Vite, TypeScript, Tailwind, lint, test, CI theo plan; Tech Lead duyệt danh sách dependency trước khi cài (A4).
5.2
BA, Dev, AI
T-02: BA đặt test vector vào src/lib/loan/__fixtures__/golden.ts. Dev chạy Prompt 5.2: viết test trước, thấy fail, rồi cài đặt đến khi pass. Dev đọc kỹ cách xử lý làm tròn.
5.3
Dev, AI
T-06: chạy Prompt 5.3 để sinh token từ Figma và file design system rules  [36].
5.4
Dev, AI
T-07: với từng component, chạy Prompt 5.4 (link tới node component). Một component một PR nhỏ.
5.5
Dev, AI
T-08, T-09: với từng màn hình, chạy Prompt 5.5 (link frame). Frame lớn: get_metadata trước, rồi từng phần  [36].
5.6
Dev, AI
T-10: chạy Prompt 5.6 cho xuất PDF. Mở file PDF thật để đọc bằng mắt: dấu tiếng Việt, số tiền, ngắt trang.
5.7
Dev
Trước mỗi commit: đọc diff, chạy lint, type-check, test. Mở PR nhỏ, gắn nhãn AI-assisted, ghi “AI làm gì, đã kiểm chứng bằng gì” (A3).
5.8
Dev, Designer
Khi thiếu sót hoặc mâu thuẫn thiết kế: tạo issue theo mẫu bên dưới (AI có thể soạn nháp, dev gửi). Designer trả lời và đặt lại Ready for dev; dev chạy lại get_design_context cho node đó  [44].

 
Prompt 5.2 (logic tính toán, test trước)
Đọc @specs/<feature>/spec.md (FR-01..) và @src/lib/loan/__fixtures__/golden.ts (do BA
  cung cấp, KHÔNG sửa file này).
Vào plan mode: đề xuất thiết kế module src/lib/loan (hàm thuần, tiền VND là số nguyên,
  làm tròn theo decision-log). Chờ mình duyệt.
Sau khi duyệt: viết test trước, chạy để thấy fail, rồi cài đặt đến khi mọi test pass.
Thêm test bất biến: tổng gốc = số tiền vay; dư nợ cuối = 0; tổng phải trả = gốc + tổng
  lãi;
lãi từng kỳ ở phương thức giảm dần không tăng. Báo cáo kết quả lệnh test.
 
Prompt 5.3 (token và rules)
Với <FIGMA_FRAME_URL> của foundations: dùng get_variable_defs, sinh
  src/styles/tokens.css và theme Tailwind chỉ dùng các token đó.
Sau đó chạy prompt create_design_system_rules của Figma MCP, lưu kết quả vào
  docs/design-system-rules.md
và thêm @docs/design-system-rules.md vào CLAUDE.md. Cho mình xem plan trước khi sửa
  file.
 
Prompt 5.4 (component)
Triển khai component Button từ <FIGMA_NODE_URL> bằng get_design_context và
  get_screenshot.
React + Tailwind, dùng token trong tokens.css, đặt ở src/components/ui/Button.tsx; mọi
  variant và state trong Figma phải có.
Không tạo style ngoài token. Viết test render cho từng variant và chạy. Cho mình xem
  plan trước.
 
Prompt 5.5 (màn hình)
Triển khai màn hình <FIGMA_FRAME_URL> (S3 Kết quả) bằng các component trong
  src/components/ui và logic trong src/lib/loan.
Frame lớn: gọi get_metadata lấy cấu trúc rồi get_design_context theo từng phần. Không
  hard-code màu hay spacing.
Sau khi xong: chạy dev server, dùng Playwright MCP chụp màn hình ở 360, 768, 1280px,
  so với get_screenshot của Figma,
liệt kê khác biệt rồi sửa.
 
Prompt 5.6 (xuất PDF)
Triển khai xuất PDF theo frame PDF Report A4 <FIGMA_FRAME_URL> bằng pdfmake. Yêu cầu:
  nhúng font TTF có glyph tiếng Việt
(nêu license), bảng kế hoạch trả nợ nhiều trang với header lặp lại, số liệu lấy từ
  cùng hàm tính với màn hình (không tính lại),
tên file loan-report-YYYYMMDD.pdf. Viết test: tạo được file, chứa đủ tiêu đề và tổng
  lãi, đúng số trang với kỳ hạn tối đa theo spec.
Đề xuất dependency và font trước; chờ duyệt.
 
Mẫu issue Dev gửi Designer (nhãn design-question)
Tiêu đề: [design-question] <màn hình/component>: <vấn đề ngắn>
Figma: <link tới node>    	Task/PR liên quan: #<số>
Hiện trạng (ảnh chụp): ...
Câu hỏi hoặc mâu thuẫn: ...
Phương án đề xuất của dev (nếu có): ...
Mức độ: Blocker / Major / Minor    	Hạn trả lời: <giờ>
Checklist G5 (mỗi PR, người viết tự kiểm):
[ ] 	Đã đọc toàn bộ diff; không có thay đổi ngoài phạm vi task.
[ ] 	lint, type-check, test, build xanh; ảnh chụp so với Figma đính kèm với PR giao diện.
[ ] 	Không hard-code màu hay spacing; tiền không dùng số thực; không dependency ngoài danh sách đã duyệt.
 
Pha 6. Review và vòng phản hồi (khoảng 0,5 đến 1 ngày, xen kẽ pha 5)
Vào: PR từ pha 5. Ra: bản preview được review người và designer chấp thuận. Cổng G6.
Bước
Ai
Việc cần làm
6.1
CI
Mỗi PR chạy lint, type-check, test, build và Playwright E2E; PR không merge nếu fail (A5).
6.2
Dev, AI
Trước khi mở PR, mở phiên review mới (subagent hoặc /code-review) với Prompt 6.2  [31].
6.3
Reviewer
Review người (khác tác giả): so kết quả với test vector, kiểm bảo mật (không log dữ liệu), dependency mới, truy vết FR.
6.4
Program Owner, Tech Lead
Tuỳ chọn: bật review tự động bằng claude-code-action trên PR. Cần duyệt vì là agent chạy nền (Cấp 3), cần secret API key, giới hạn quyền của workflow và theo dõi chi phí  [45].
6.5
Designer
Xem bản preview ở 360, 768, 1280px, so với Figma. Ghi feedback vào PR hoặc issue với mức độ (Blocker/Major/Minor) và link node. Không sửa code trực tiếp.
6.6
Dev, AI
Gom feedback thành danh sách đánh số, chạy Prompt 6.6. Sau hai lần sửa không xong thì /clear và viết lại prompt  [31].
6.7
Dev, Designer
Nếu lệch thiết kế là có chủ đích: ghi decision-log và để Designer cập nhật Figma. Tuỳ chọn: dùng generate_figma_design đưa UI chạy thật vào Figma làm bằng chứng  [36].

 
Prompt 6.2 (review độc lập)
Dùng một subagent review diff của nhánh này so với @specs/<feature>/spec.md và
  plan.md. Kiểm: các FR của task đã đủ;
trường hợp biên có test; không có thay đổi ngoài phạm vi task; tiền không dùng số
  thực; không hard-code màu hay spacing.
Chỉ báo lỗ hổng ảnh hưởng tới đúng/sai hoặc yêu cầu; bỏ qua sở thích về phong cách.
 
Prompt 6.6 (xử lý feedback)
Đây là danh sách feedback đánh số cho PR #<n>: <dán danh sách>.
Vào plan mode: với mỗi mục nêu nguyên nhân, cách sửa, file bị chạm, và test hoặc ảnh
  chụp sẽ chứng minh.
KHÔNG sửa gì khi chưa được duyệt. Sau khi duyệt: sửa từng mục, chạy kiểm tra, trả lời
  theo số mục.
Checklist G6:
[ ] 	Mọi Blocker và Major đã đóng; reviewer khác tác giả đã approve.
[ ] 	Designer sign-off giao diện ở 3 breakpoint (ghi trên PR).
[ ] 	Mọi lệch thiết kế có chủ đích đã nằm trong decision-log và Figma đã cập nhật.
 
Pha 7. Kiểm thử và nghiệm thu (khoảng 0,5 đến 1 ngày)
Vào: bản preview hoặc staging, qa-plan. Ra: báo cáo test truy vết FR và biên bản UAT. Cổng G7.
Bước
Ai
Việc cần làm
7.1
QA, AI
Sinh ma trận test từ spec (FR → ca test) bằng prompt ngắn dựa trên qa-plan.md; QA bổ sung ca còn thiếu.
7.2
QA hoặc Dev, AI
Viết Playwright E2E: nhập, tính, xem bảng, tải PDF; kiểm tra đầu vào; hai phương thức; responsive. Cho agent khám phá UI bằng Playwright MCP trước khi viết test  [46]. Kiểm tra nội dung PDF cần thêm thư viện đọc PDF trong test: Tech Lead duyệt dependency.
7.3
QA
Kiểm thử thủ công và đối chiếu song song với công cụ tham chiếu  [52] với cùng đầu vào (ít nhất 5 ca, gồm Ca A). Mỗi chênh lệch phải được giải thích (làm tròn, khác phương thức, hay lỗi) và ghi decision-log.
7.4
QA
Kiểm tra truy cập cơ bản: thao tác bằng bàn phím, focus nhìn thấy, tương phản, nhãn form  [53].
7.5
PO, client
UAT trên preview hoặc staging. Ghi biên bản: ngày, phiên bản, phát hiện, xác nhận chấp nhận.
7.6
AI, QA
AI tổng hợp báo cáo test truy vết FR → test → kết quả; QA kiểm lại trước khi gửi.

Checklist G7:
[ ] 	Mọi FR Must có test pass; mọi chênh lệch với công cụ tham chiếu đã được giải thích.
[ ] 	PDF mở được trên ít nhất hai trình đọc, dấu tiếng Việt đúng, số liệu khớp màn hình.
[ ] 	Client xác nhận chấp nhận bằng văn bản.
 
Pha 8. Phát hành production (khoảng 0,25 ngày; do người thực hiện)
Vào: bản đã qua G7. Ra: production chạy ổn, có kế hoạch rollback. Cổng G8. Agent không giữ credential và không deploy (A4).
Bước
Ai
Việc cần làm
8.1
Tech Lead
Lập checklist phát hành: truy vết FR đầy đủ, tag phiên bản, changelog, biến môi trường (không có), dòng miễn trừ, tên miền, quyền truy cập, kế hoạch rollback. AI có thể soạn nháp checklist và release notes.
8.2
Tech Lead (và Director nếu client-facing)
Go/No-Go: duyệt bằng văn bản (approval trên PR phát hành hoặc environment).
8.3
Người được chỉ định
Deploy. Với Vercel: preview đã có từ PR; promote theo quy trình đã chọn, hoặc merge vào nhánh production (tự promote), hoặc dựng staged build rồi bấm Promote  [48]. Nếu deploy bằng GitHub Actions, dùng Environment có required reviewers khi gói hỗ trợ  [47].
8.4
Người được chỉ định
Smoke test trên production: mở trang, tính hai ca (một ca mỗi phương thức), tải PDF, kiểm tra log lỗi.
8.5
Tech Lead
Ghi cách rollback (Instant Rollback của Vercel  [48]) vào handoff pack; xác nhận đã thử ở staging.

Checklist G8:
[ ] 	Go/No-Go đã được duyệt; người deploy là người, không phải agent.
[ ] 	Smoke test production đạt; kế hoạch rollback đã được thử.
 
Pha 9. Bàn giao và retro (khoảng 0,25 ngày)
Vào: sản phẩm đã chạy production. Ra: handoff pack, biên bản bàn giao, rules và template được cập nhật. Cổng G9.
Bước
Ai
Việc cần làm
9.1
AI, Dev
AI soạn handoff pack trong docs/09-handoff/: README (chạy, build, test), kiến trúc, spec và decision-log, link Figma, vận hành và rollback, hạn chế đã biết, backlog đề xuất.
9.2
Dev khác
Kiểm bằng clone sạch trên máy khác: làm đúng theo README. Lệnh nào không chạy thì sửa tài liệu.
9.3
BA/PO
Buổi bàn giao với client; nhận xác nhận bàn giao.
9.4
Cả nhóm
Retro 30 phút với số liệu: thời gian mỗi pha, số vòng sửa, lỗi sau phát hành, chi phí AI, số lần agent bị chặn hoặc cần can thiệp. Chuyển bài học vào CLAUDE.md, template, prompt library; gửi Program Owner cho đánh giá tháng 3 (mục 8).

Checklist G9:
[ ] 	Handoff pack đã kiểm bằng clone sạch; client xác nhận bàn giao.
[ ] 	Retro có số liệu; rules, template, prompt đã được cập nhật.
 
B8. Mẫu dùng chung
 
Decision log (docs/01-analysis/decision-log.md)
| ID	| Ngày       | Câu hỏi / vấn đề     	| Quyết định       	| Người quyết |
  Ảnh hưởng (FR/Task) |
| D-001 | <dd/mm/yy> | <ví dụ: quy tắc làm tròn> | <nội dung quyết định> | <tên, vai
  trò> | FR-xx, T-02    	|
 
Change Request (sau G2)
CR-<số> | Ngày: <...> | Người đề xuất: <...>
Thay đổi yêu cầu (FR/NFR liên quan): ...
Lý do và giá trị: ...
Ảnh hưởng: thiết kế (frame nào), code (module nào), test, tiến độ, chi phí.
Quyết định: Chấp nhận / Từ chối / Hoãn   Người duyệt: PO + Tech Lead   Ngày: <...>
Sau khi chấp nhận: sửa spec bằng PR, tăng phiên bản (spec-v1.1), cập nhật tasks.
 
PR template (.github/pull_request_template.md)
## Thay đổi gì
## AI đã làm gì (công cụ, phạm vi) / Con người đã làm gì
## Đã kiểm chứng bằng gì (lệnh test, ảnh chụp so với Figma)
- [ ] AI-assisted   - [ ] Đã đọc toàn bộ diff   - [ ] Không có thay đổi ngoài phạm vi
  task   - [ ] FR liên quan: ...
B9. Áp dụng cho dự án khác
• 	Giữ nguyên 10 pha và các cổng; thay use case, test vector và danh sách câu hỏi mở. Phần nghiệp vụ nhạy cảm (tài chính, y tế, pháp lý) luôn cần test vector do con người tính độc lập.
• 	Dự án không có giao diện: bỏ P4 và các bước Figma, giữ P1-P3 và P5-P9. Dự án có backend: thêm bước thiết kế API (spec contract) vào P3 và kiểm thử tích hợp vào P7.
• 	Sau mỗi dự án, cập nhật phụ lục này từ kết quả retro để quy trình tốt dần lên.
