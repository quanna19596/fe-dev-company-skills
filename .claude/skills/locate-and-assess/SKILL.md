---
name: locate-and-assess
description: Dùng trước khi code một tính năng trong dự án legacy, khi cần tìm tính năng nằm ở đâu trong code, điểm bắt đầu phát triển, có component/hàm/hook/service nào tái sử dụng được không, hoặc khi cân nhắc có nên sửa code cũ; khi người dùng nói "khoanh vùng", "tìm chỗ code", "đánh giá tái sử dụng", "có sửa code cũ được không".
---

# Khoanh vùng & đánh giá tái sử dụng

## Mục tiêu

Tìm điểm bắt đầu phát triển trong code. Quyết định từng thứ cần dùng nên tái sử dụng, sửa nhẹ hay viết mới. Lập kế hoạch triển khai để người dùng duyệt trước khi code.

**Nguyên tắc cốt lõi:** dự án legacy, không có tài liệu, không hỏi được người viết code cũ.
- Hành vi hiện có của code cũ được coi là nghiệp vụ đúng cho tới khi BA nói khác.
- Nhưng cũng không được tạo bản trùng lặp của thứ đã có.

## Chuẩn bị

- Đọc `CLAUDE.local.md`, mục "Bản đồ code".
- Đọc ghi chú tính năng. Nếu chưa có phân tích yêu cầu, dùng skill `analyze-requirements` trước.

## 1. Tìm điểm bắt đầu

Dùng nhiều cách tìm đối chiếu với nhau:
- **Route:** tìm path của màn hình trong nơi khai báo route, rồi đi tới page component.
- **Text trên UI:** tìm các nhãn, tiêu đề trong Figma/BRD trong code, trong file i18n (nếu có), rồi đi tới component.
- **API:** tìm các endpoint liên quan có sẵn trong tầng service.

Kết quả cần có: page component, các component con liên quan, nơi quản lý state, nơi gọi API. Ghi rõ đường dẫn file.

## 2. Kiểm kê thứ có thể tái sử dụng

Với mỗi thứ tính năng cần (component UI, hook, hàm tiện ích, service API, type, hằng số), tìm xem dự án đã có thứ tương tự chưa. Tìm theo tên, theo cấu trúc JSX hoặc style, theo endpoint.

Với mỗi ứng viên tìm được:
1. **Đọc kỹ** nó làm gì, nhận vào gì, trả ra gì.
2. **Liệt kê mọi nơi đang dùng nó.** Tìm theo import và theo tên.
3. **Phân loại:**

| Loại | Điều kiện | Cần làm |
|---|---|---|
| Dùng nguyên | Đáp ứng đúng nhu cầu, không cần sửa | Dùng luôn |
| Sửa nhẹ an toàn | Chỉ **thêm** prop hoặc tham số tuỳ chọn, giá trị mặc định giữ nguyên hành vi cũ. Mọi nơi đang dùng không bị ảnh hưởng. | Ghi rõ sẽ thêm gì, vì sao an toàn |
| Sửa code cũ | Đổi hành vi mặc định, đổi signature, đổi style hoặc logic mà nơi khác đang dùng | **Phải hỏi BA trước** (xem bước 3) |
| Viết mới | Khác quá nhiều so với thứ đang có | Ghi lý do không tái sử dụng được |

## 3. Câu hỏi cho BA khi phải sửa code cũ

Với mỗi chỗ thuộc loại "Sửa code cũ", soạn câu hỏi gồm:
- Muốn sửa gì và vì sao tính năng mới cần sửa.
- Những màn hình hoặc luồng nào đang dùng chỗ đó. Gọi theo tên nghiệp vụ và đường dẫn, không gọi theo tên file.
- Sau khi sửa, các màn hình đó sẽ thay đổi thế nào, có thể phát sinh trường hợp gì.
- Phương án thay thế nếu BA không đồng ý.

Nếu BA không chắc hoặc không đồng ý, dùng phương án thay thế.

## 4. Chỗ lệch giữa code và Figma

So sánh thông số trong Figma với component và design token đang có (màu, bo góc, khoảng cách, font).
- Nếu lệch, **không tự sửa theo Figma**. Soạn câu hỏi cho Designer, ví dụ: "Button trong hệ thống đang bo góc 4px, Figma của tính năng này là 6px. Có cần đổi thành 6px không, hay giữ 4px theo design system?"
- Ghi rõ nếu đổi thì sẽ ảnh hưởng tới những đâu.

## Đầu ra: kế hoạch triển khai

Trình bày theo thứ tự sau:
1. **Điểm bắt đầu:** các file chính.
2. **Tái sử dụng:** dùng nguyên và sửa nhẹ an toàn, mỗi mục kèm danh sách nơi đang dùng.
3. **Sửa code cũ (cần BA duyệt):** kèm câu hỏi đã soạn.
4. **Viết mới:** file dự kiến tạo, đặt ở đâu theo cấu trúc hiện có của dự án.
5. **Câu hỏi cho Designer** (nếu có).

Chưa bắt đầu code khi người dùng chưa duyệt kế hoạch.

Hỏi người dùng có cho lưu kế hoạch vào mục "Kế hoạch triển khai" trong ghi chú tính năng không. Khi có câu trả lời của BA hoặc Designer thì cập nhật vào ghi chú.

## Lỗi thường gặp

| Sai | Đúng |
|---|---|
| Thấy component tên giống liền dùng luôn | Đọc kỹ, liệt kê mọi nơi đang dùng rồi mới phân loại |
| Sửa giá trị mặc định của component dùng chung "cho đúng Figma" | Đó là sửa code cũ, phải hỏi BA hoặc Designer |
| Sợ động vào code cũ nên viết lại một bản gần giống | Nếu chỉ cần thêm prop tuỳ chọn thì tái sử dụng |
| Câu hỏi cho BA nêu tên file, tên component | Nêu tên màn hình, luồng nghiệp vụ |
