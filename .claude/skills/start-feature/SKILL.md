---
name: start-feature
description: Dùng khi cần tạo nhánh mới để bắt đầu làm một tính năng; khi người dùng nói "checkout nhánh", "tạo nhánh", "tạo branch", "bắt đầu code tính năng".
---

# Tạo nhánh tính năng

## Mục tiêu

Tạo nhánh tính năng đúng chỗ, đúng tên, theo chiến lược git của dự án.

**Nguyên tắc cốt lõi:** mọi lệnh git làm thay đổi trạng thái đều phải trình bày trước và chờ người dùng đồng ý, kể cả `git fetch` và `git checkout`.

## Chuẩn bị

1. Đọc `CLAUDE.local.md`, các mục "Môi trường & nhánh", "Chiến lược git", "Quy ước git".
   - Mục nào còn thiếu thì hỏi người dùng.
2. Đọc ghi chú tính năng (nếu có) để lấy mã ticket.
3. Chạy `git status` và `git branch --show-current` (chỉ đọc).
   - Nếu working tree đang có thay đổi chưa commit, dừng lại. Báo người dùng biết đang có những file nào thay đổi và hỏi xử lý thế nào: commit, stash, hay bỏ. Không tự stash, không tự xoá.

## Chọn nhánh gốc

| Chiến lược | Tạo nhánh nào lúc này | Từ nhánh nào |
|---|---|---|
| Chuẩn | Một nhánh tính năng | Nhánh môi trường cao nhất |
| Cherry-pick | Chỉ nhánh A1 (môi trường thấp nhất) | Nhánh của môi trường thấp nhất (A) |

Với chiến lược cherry-pick, các nhánh B1, C1 chưa tạo ở bước này. Skill `promote-env` sẽ tạo chúng khi đến lượt.

## Đề xuất lệnh

Trình bày đầy đủ các lệnh sẽ chạy, ví dụ:

```bash
git fetch origin
git checkout -b <tên nhánh theo quy ước> origin/<nhánh gốc>
```

Tạo nhánh từ `origin/<nhánh gốc>` để có code mới nhất trên remote, không phụ thuộc nhánh local có thể đã cũ.

Người dùng đồng ý thì chạy, sau đó kiểm tra bằng:
- `git branch --show-current`
- `git log --oneline -1`: phải trùng với commit mới nhất của `origin/<nhánh gốc>`.

## Ghi lại

Hỏi người dùng có cho ghi vào bảng "Nhánh & commit" trong ghi chú tính năng không. Được đồng ý thì ghi:
- tên nhánh tính năng;
- nhánh gốc;
- **commit gốc**, lấy bằng `git rev-parse origin/<nhánh gốc>` ngay sau khi tạo nhánh.

Commit gốc là mốc để `write-report`, `fix-bug`, `promote-env` biết đâu là các commit của tính năng, kể cả sau khi đã merge.

## Lỗi thường gặp

| Sai | Đúng |
|---|---|
| Tạo nhánh từ nhánh local đang đứng | Tạo từ `origin/<nhánh gốc>` sau khi fetch |
| Với chiến lược cherry-pick, tạo luôn cả A1, B1, C1 | Chỉ tạo A1. B1, C1 tạo khi promote |
| Tự stash thay đổi đang dở | Báo người dùng và hỏi cách xử lý |
