---
name: fix-bug
description: Dùng khi tester log bug trên Jira cho tính năng đang làm và người dùng paste nội dung bug vào; khi nói "fix bug", "có bug", "tester báo lỗi", "sửa lỗi", hoặc đưa vào một ticket bug kèm hiện trạng và mong muốn.
---

# Fix bug do tester log

## Mục tiêu

Tìm đúng nguyên nhân gốc của bug và sửa ngay trên nhánh tính năng, với thay đổi nhỏ nhất có thể. Sau đó đẩy code lên lại.

**Nguyên tắc cốt lõi:**
- Chưa tìm ra nguyên nhân thì chưa sửa.
- Không phải mọi thứ tester log đều là bug của tính năng mới. Có thể đó là hành vi sẵn có của code cũ, hoặc tester mong muốn khác với BRD.

## 1. Thu thập thông tin

Xin người dùng paste nội dung bug gồm: mã bug, môi trường, các bước tái hiện, hiện trạng, mong muốn, ảnh hoặc video (nếu có).

Đọc ghi chú tính năng để biết bối cảnh: BRD, các câu trả lời đã có, các commit.

## 2. Phân loại trước khi sửa

So sánh "mong muốn" của tester với BRD, Figma và các câu trả lời đã có trong ghi chú:

| Trường hợp | Cách xử lý |
|---|---|
| Code làm sai so với tài liệu | Bug của tính năng, sửa |
| Mong muốn của tester khác với BRD | Không sửa. Soạn câu hỏi cho BA, kèm trích dẫn đoạn BRD liên quan |
| Lỗi nằm ở phần code cũ, đã có từ trước khi làm tính năng | Báo người dùng. Soạn câu trả lời cho tester và câu hỏi cho BA xem có cần sửa không. Sửa thì phải được BA đồng ý |
| Tài liệu không nói rõ | Hỏi BA hoặc Designer trước khi sửa |

Để biết lỗi có từ trước hay không, xem đoạn code gây lỗi có nằm trong diff của nhánh tính năng không: `git diff <commit gốc> HEAD`, commit gốc lấy trong ghi chú tính năng. Có thể dùng thêm `git log -L` hoặc `git blame` cho đoạn code đó.

## 3. Chọn nhánh để sửa

- **Chiến lược chuẩn:** sửa trên nhánh tính năng.
- **Chiến lược cherry-pick, bug ở môi trường thấp nhất (A):** sửa trên A1.
- **Chiến lược cherry-pick, bug ở môi trường cao hơn (B, C):** hỏi người dùng nên sửa trên nhánh nào trước, rồi đưa commit sang các nhánh còn lại như thế nào. Hồ sơ dự án chưa có quy tắc cho trường hợp này. Khi người dùng trả lời, hỏi có cho ghi quy tắc đó vào hồ sơ dự án không.

Kiểm tra đang đứng đúng nhánh và working tree sạch. Cần chuyển nhánh thì hỏi người dùng trước.

## 4. Tìm nguyên nhân gốc

1. Lần theo các bước tái hiện trong code: từ thao tác của người dùng, qua handler và state, tới lời gọi API và phần render.
2. Nêu giả thuyết về nguyên nhân, kèm bằng chứng: dòng code nào, dữ liệu nào.
   - Cần thêm thông tin thì xin người dùng, ví dụ response API thực tế, log trên console, phiên bản trình duyệt.
3. Nếu lỗi nằm ở phía BE (response sai so với BE Specs), không vá ở FE. Soạn câu hỏi cho BE kèm bằng chứng.

## 5. Sửa

- Sửa đúng nguyên nhân gốc, phạm vi nhỏ nhất.
- Không tiện tay refactor phần xung quanh.
- Chạy lint, type-check, test theo hồ sơ dự án.
- Trình bày cho người dùng: nguyên nhân, cách sửa, file nào bị sửa.

## 6. Đẩy code lên

Dùng skill `ship-changes`. Commit message ghi mã bug, ví dụ `fix(<mã bug>): ...`.

Sau đó:
- Hỏi người dùng có cho ghi vào bảng "Bug" trong ghi chú tính năng không (mã bug, môi trường, nguyên nhân, commit fix).
- Nếu người dùng muốn, soạn sẵn comment để trả lời tester trên Jira: nguyên nhân, đã sửa ở commit nào, cần chờ deploy.

## Lỗi thường gặp

| Sai | Đúng |
|---|---|
| Đọc bug xong sửa ngay chỗ hiện ra lỗi | Tìm nguyên nhân gốc trước |
| Làm theo mong muốn của tester dù trái với BRD | Hỏi BA |
| Sửa luôn lỗi nằm trong code cũ | Báo lại, BA đồng ý mới sửa |
| Response API sai nên vá dữ liệu ở FE | Báo BE kèm bằng chứng |
