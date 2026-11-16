---
name: implement-feature
description: Dùng khi bắt đầu viết code cho một tính năng frontend React/TypeScript đã có kế hoạch triển khai; khi người dùng nói "code tính năng", "implement", "làm màn hình", "dựng UI theo Figma", "tích hợp API".
---

# Code tính năng

## Mục tiêu

Code tính năng đúng BRD, đúng Figma, đúng BE Specs. Viết theo đúng phong cách sẵn có của dự án, và chỉ động vào những chỗ đã được duyệt.

## Điều kiện trước khi code

Kiểm tra lần lượt. Thiếu điều kiện nào thì dừng lại và dùng skill tương ứng:

| Điều kiện | Nếu thiếu |
|---|---|
| Có `CLAUDE.local.md` | `onboard-project` |
| Ghi chú tính năng có phân tích yêu cầu | `analyze-requirements` |
| Kế hoạch triển khai đã được người dùng duyệt | `locate-and-assess` |
| Đang đứng đúng nhánh tính năng (`git branch --show-current`) | `start-feature` |

Phần nào của tính năng còn phụ thuộc câu hỏi chưa có câu trả lời thì để lại sau, làm các phần khác trước. Báo người dùng biết đang để lại phần nào.

## Khi code

1. **Theo khuôn mẫu sẵn có.** Trước khi tạo file mới, xem 1–2 màn hình hoặc module tương tự trong dự án và làm theo:
   - cấu trúc thư mục, cách đặt tên;
   - cách viết style;
   - cách gọi API, quản lý state, viết form và validation;
   - i18n (nếu dự án có).

   Theo khuôn mẫu của dự án, kể cả khi có cách làm "hiện đại hơn".
2. **Phạm vi được sửa:** chỉ sửa các chỗ trong kế hoạch, gồm phần sửa nhẹ an toàn và phần sửa code cũ đã được BA đồng ý.
   - Phát sinh thêm chỗ cần sửa code cũ thì dừng lại. Đánh giá theo bước 2–3 của skill `locate-and-assess`, rồi hỏi người dùng.
   - Không refactor, không format lại, không sửa lỗi lint ở code cũ không liên quan tới tính năng.
3. **Theo Figma:**
   - Nếu có Figma MCP, lấy thông số chính xác từ đó.
   - Ưu tiên dùng design token và component đang có thay vì viết cứng giá trị.
   - Gặp chỗ lệch với design system thì hỏi Designer (mục 4 của `locate-and-assess`), không tự quyết.
4. **Theo BE Specs:**
   - Khai báo type TypeScript cho request và response theo BE Specs.
   - Xử lý đủ các trạng thái: loading, rỗng, lỗi.
   - Thông báo lỗi lấy theo BRD.
5. **Test:** viết unit test chỉ khi hồ sơ dự án yêu cầu. Nếu có thì viết theo cách test sẵn có của dự án.

## Khi code xong

1. Chạy các lệnh lint, type-check, test có trong hồ sơ dự án.
   - Lỗi nằm trong code mình viết thì sửa.
   - Lỗi có sẵn từ trước trong code cũ thì chỉ báo lại, không sửa.
2. Nếu dự án có thể chạy local, đề xuất người dùng chạy thử, hoặc tự chạy để kiểm tra màn hình nếu người dùng đồng ý.
3. Tổng kết cho người dùng:
   - các file tạo mới, các file đã sửa;
   - phần nào chưa làm và vì sao (đang chờ câu hỏi nào);
   - kết quả các lệnh kiểm tra.
4. Không commit. Việc commit do skill `ship-changes` làm.

## Lỗi thường gặp

| Sai | Đúng |
|---|---|
| Viết cứng `#1A73E8`, `6px` | Dùng token hoặc component sẵn có, lệch thì hỏi |
| Đang code tiện tay sửa luôn code cũ "cho đẹp" | Chỉ sửa đúng các chỗ trong kế hoạch |
| Tự chế thông báo lỗi khi BRD không nói | Hỏi BA |
| Báo "xong" khi chưa chạy lint, type-check | Chạy xong, có kết quả rồi mới báo |
| Dùng thư viện mới chưa có trong dự án | Hỏi người dùng trước khi thêm dependency |
