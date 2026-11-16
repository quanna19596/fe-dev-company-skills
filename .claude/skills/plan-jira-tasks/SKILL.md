---
name: plan-jira-tasks
description: Dùng khi người dùng cần chia task hoặc tạo task trên Jira cho một tính năng; khi nói "chia task", "tạo task Jira", "break task", "lên đầu việc", "estimate".
---

# Chia task Jira

## Mục tiêu

Soạn sẵn danh sách task để người dùng tự paste vào Jira. Claude không kết nối Jira.

**Nguyên tắc cốt lõi:** cấu trúc, cách đặt tên, ngôn ngữ và các trường bắt buộc đều theo quy ước của PM ghi trong `CLAUDE.local.md`. Không dùng quy ước của dự án khác.

## Chuẩn bị

1. Đọc `CLAUDE.local.md`, mục "Quy ước Jira".
   - Nếu mục nào còn trống hoặc `CHƯA XÁC NHẬN`, hỏi người dùng trước.
   - Câu trả lời của người dùng được ghi lại vào hồ sơ (hỏi người dùng trước khi ghi).
2. Đọc ghi chú tính năng `.claude/features/<mã>.md`.
   - Nếu chưa có, dùng skill `analyze-requirements` trước. Không chia task khi chưa hiểu phạm vi tính năng.

## Cách chia

- Đề xuất cách chia dựa trên danh sách màn hình và hành vi trong ghi chú tính năng.
- Mỗi task phải có kết quả kiểm tra được: xong task là tester hoặc người dùng thấy được điều gì đó.
- Task nào còn phụ thuộc vào câu hỏi chưa có câu trả lời (BA, Designer, BE) thì đánh dấu `Bị chặn bởi câu hỏi #<n>`.
- Trước khi soạn chi tiết, trình bày cách chia (danh sách tên task) để người dùng điều chỉnh.

## Đầu ra

Sau khi người dùng đồng ý cách chia, soạn từng task trong một khối riêng để copy cho dễ. Các trường lấy theo quy ước của PM. Nếu PM không quy định gì thì dùng tối thiểu các trường sau:

```
Summary: <tên task>
Description:
<mô tả: làm gì, ở màn hình nào, tham chiếu mục nào trong BRD / frame nào trong Figma / endpoint nào>
Acceptance criteria:
- <tiêu chí 1>
- <tiêu chí 2>
```

## Sau khi người dùng đã tạo task trên Jira

Xin người dùng các mã task, rồi ghi vào mục "Trạng thái" của ghi chú tính năng.
- Nếu file ghi chú đang đặt theo tên tạm, hỏi người dùng có cho đổi tên file theo mã ticket chính không.

## Lỗi thường gặp

| Sai | Đúng |
|---|---|
| Tự đặt cấu trúc Epic/Story/Sub-task | Theo quy ước của PM trong hồ sơ dự án, chưa có thì hỏi |
| Một task "Làm màn hình X" bao hết mọi thứ | Chia theo phần kiểm tra được, kèm acceptance criteria |
| Bỏ qua các câu hỏi còn chưa trả lời | Đánh dấu task bị chặn bởi câu hỏi |
