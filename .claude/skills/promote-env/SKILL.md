---
name: promote-env
description: Dùng khi tester đã xác nhận hết bug ở môi trường kiểm thử hiện tại và cần đưa tính năng lên môi trường cao hơn hoặc chuẩn bị golive production; khi người dùng nói "lên môi trường", "promote", "golive", "MR lên production", "cherry-pick sang nhánh".
---

# Đưa tính năng lên môi trường cao hơn

## Mục tiêu

Đưa đầy đủ các commit của tính năng (gồm cả các commit fix bug) lên môi trường kế tiếp, hoặc lên production để golive, theo chiến lược git của dự án.

**Nguyên tắc cốt lõi:**
- Mỗi lệnh git làm thay đổi trạng thái đều hỏi trước.
- Không được bỏ sót commit. Không được mang theo commit không thuộc tính năng.

## Chuẩn bị

- Đọc `CLAUDE.local.md` (môi trường, chiến lược git) và ghi chú tính năng (nhánh, commit, bug).
- Hỏi người dùng xác nhận: tester đã xác nhận hết bug ở môi trường hiện tại chưa, và môi trường đích là môi trường nào.

## Chiến lược chuẩn

1. Kiểm tra nhánh tính năng trên remote đã có đủ commit, gồm cả các commit fix bug.
2. Soạn merge request từ nhánh tính năng vào nhánh của môi trường đích, theo mẫu ở skill `ship-changes`.
3. Nếu đích là production thì ghi rõ trong mô tả rằng merge request này chờ lịch golive.

## Chiến lược cherry-pick

Ví dụ đưa từ A lên B. Từ B lên C (golive) làm y hệt, thay A1 bằng B1 và B1 bằng C1.

1. **Lấy danh sách commit cần mang theo.**
   - Chạy `git log --no-merges --reverse --oneline <commit gốc của A1>..<A1>`. Commit gốc lấy trong ghi chú tính năng. Dùng nhánh A1 ở local, vì nhánh trên remote có thể đã bị xoá sau khi merge. Trước khi chạy, kiểm tra A1 ở local đã có đủ các commit đã push.
   - **Không dùng `origin/<A>` làm mốc.** Khi merge request A1→A đã merge, `origin/<A>` đã chứa sẵn các commit này nên kết quả sẽ rỗng.
   - Nếu ghi chú không có commit gốc: tìm bằng `git reflog show <A1>` (dòng tạo nhánh), hoặc hỏi người dùng.
   - **Đối chiếu kết quả với danh sách commit trong ghi chú,** rồi trình bày cho người dùng xác nhận.
   - Nếu A1 từng merge nhánh A vào, danh sách có thể lẫn commit của người khác. Chỉ giữ commit của tính năng.
2. **Tạo B1** (hỏi người dùng trước):
   ```bash
   git fetch origin
   git checkout -b <tên nhánh B1 theo quy ước> origin/<B>
   ```
   Ghi lại commit gốc của B1: `git rev-parse origin/<B>`.
3. **Cherry-pick theo đúng thứ tự cũ → mới** (hỏi người dùng trước):
   ```bash
   git cherry-pick <hash1> <hash2> ...
   ```
4. **Nếu có conflict,** dừng lại.
   - Trình bày file bị conflict và nội dung hai phía.
   - Giải thích vì sao conflict: thường do nhánh B khác A ở đoạn code đó.
   - Đề xuất cách giải quyết, chờ người dùng đồng ý rồi mới `git cherry-pick --continue`.
   - Không tự chọn `ours`/`theirs` hàng loạt.
   - Nếu cần rebase, giải thích lý do và hỏi người dùng trước.
5. **Kiểm tra kết quả:**
   - Chạy `git range-diff <commit gốc A1>..<A1> <commit gốc B1>..HEAD` để so các commit trên A1 với các commit trên B1. Mọi khác biệt (ngoài phần đã xử lý conflict) đều phải giải thích được.
   - Chạy lint, type-check, test theo hồ sơ dự án.
6. **Push và soạn merge request** B1 → B, theo bước 3–4 của skill `ship-changes`. Push phải hỏi riêng.
7. Hỏi người dùng có cho ghi vào ghi chú tính năng không: nhánh B1, commit gốc của B1, danh sách commit mới (hash trên B1). Bước sau, khi lên C, sẽ cherry-pick từ B1.

## Sau khi lên môi trường mới

- Cập nhật mục "Trạng thái" trong ghi chú tính năng (hỏi người dùng trước).
- Nhắc người dùng thông báo cho tester khi deploy xong.
- Nếu đây là bước golive, gợi ý dùng skill `write-report` để viết báo cáo.

## Lỗi thường gặp

| Sai | Đúng |
|---|---|
| Chỉ pick commit tính năng, quên các commit fix bug | Lấy toàn bộ commit trên A1 kể từ commit gốc, đối chiếu với ghi chú |
| Dùng `origin/<A>..A1` sau khi đã merge, ra danh sách rỗng | Dùng commit gốc làm mốc |
| Pick sai thứ tự | Dùng `--reverse`, đi từ commit cũ tới mới |
| Pick cả commit merge, hoặc commit của người khác | Dùng `--no-merges` và lọc chỉ lấy commit của tính năng |
| Tạo B1 từ A1 | Tạo B1 từ `origin/<B>` |
| Conflict thì chọn hết một phía | Trình bày từng chỗ và hỏi người dùng |
