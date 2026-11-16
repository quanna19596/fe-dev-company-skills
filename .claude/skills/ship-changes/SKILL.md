---
name: ship-changes
description: Dùng khi code tính năng hoặc code fix bug đã xong và cần commit, push, tạo merge request vào nhánh môi trường kiểm thử; khi người dùng nói "commit", "push", "tạo MR", "merge request", "đẩy code lên".
---

# Commit, push và soạn merge request

## Mục tiêu

Đưa code lên nhánh tính năng trên remote. Soạn sẵn nội dung merge request để người dùng tự tạo trên GitLab.

**Nguyên tắc cốt lõi:**
- Commit, push mỗi bước đều hỏi riêng. Đồng ý commit không có nghĩa là đồng ý push.
- Commit message và mô tả merge request viết bằng tiếng Anh, theo quy ước trong `CLAUDE.local.md`.

## 1. Kiểm tra trước khi commit (chỉ đọc)

- `git branch --show-current`: phải đúng nhánh tính năng ghi trong ghi chú tính năng.
  - Với chiến lược cherry-pick, lần đầu đẩy code thì code luôn nằm ở A1. Nếu đang đứng ở B1 hoặc C1, xem skill `promote-env`.
- `git status` và `git diff`: đọc lại toàn bộ thay đổi.
  - Loại ra các file không thuộc tính năng: file cấu hình local, file debug, thay đổi do lỡ tay format.
  - Không bao giờ commit `CLAUDE.local.md` hay thư mục `.claude/`.
- Chạy lint, type-check, test theo hồ sơ dự án, nếu từ lúc chạy lần trước đến giờ code đã thay đổi.

## 2. Commit

Trình bày cho người dùng:
- **Danh sách file sẽ commit.** Dùng `git add <từng file>`, không dùng `git add .` hay `git add -A`.
- **Commit message** theo quy ước của dự án. Nếu dự án chưa có quy ước, dùng mẫu:

```
<type>(<mã ticket>): <mô tả ngắn, tiếng Anh, thể mệnh lệnh>

<giải thích thay đổi gì và vì sao, nếu cần>
```

Chia commit hợp lý. Mỗi commit một ý trọn vẹn, vì sau này có thể phải cherry-pick riêng từng commit.

Người dùng đồng ý thì commit.

## 3. Push

Hỏi người dùng có cho push không. Đồng ý thì chạy `git push -u origin <nhánh tính năng>`.
- Nếu GitLab trả về đường dẫn tạo merge request trong output của lệnh push, đưa đường dẫn đó cho người dùng.
- Không dùng `--force`. Nếu push bị từ chối, báo lại nguyên nhân và hỏi cách xử lý.

## 4. Soạn merge request

Nhánh đích:
- **Chiến lược chuẩn:** nhánh của môi trường kiểm thử đang làm (lần đầu là môi trường thấp nhất).
- **Chiến lược cherry-pick:** A1 → A.

Soạn theo mẫu merge request của dự án. Nếu dự án chưa có mẫu thì dùng:

```
Title: <type>(<mã ticket>): <tóm tắt>

Source: <nhánh tính năng> → Target: <nhánh đích>

## Summary
<what this MR does>

## Changes
- <screen / module>: <change>

## Related
- Jira: <mã ticket>
```

Người dùng tự tạo merge request trên GitLab và tự merge, hoặc chờ được duyệt.

## 5. Sau khi push

- Hỏi người dùng có cho ghi hash và message của các commit vừa push vào bảng "Nhánh & commit" trong ghi chú tính năng không. Danh sách commit này cần cho `promote-env` khi cherry-pick.
- Nhắc người dùng: khi đội vận hành deploy xong, thông báo cho tester. Nếu người dùng muốn, soạn sẵn tin nhắn cho tester.

## Lỗi thường gặp

| Sai | Đúng |
|---|---|
| `git add .` | Add từng file đã kiểm tra |
| Người dùng đồng ý commit nên push luôn | Hỏi riêng trước khi push |
| Một commit gom cả tính năng lẫn fix lặt vặt không liên quan | Mỗi commit một ý, để cherry-pick được |
| Viết commit message tiếng Việt | Tiếng Anh, theo quy ước dự án |
| Push bị từ chối liền dùng `--force` | Báo lại và hỏi người dùng |
