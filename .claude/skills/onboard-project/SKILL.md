---
name: onboard-project
description: Dùng khi bắt đầu làm việc với một dự án frontend lần đầu, khi repo chưa có file CLAUDE.local.md, khi người dùng nói "tìm hiểu dự án", "onboard", "dự án mới", hoặc khi một skill khác cần hồ sơ dự án mà chưa có. Cũng dùng khi cần cập nhật hồ sơ dự án (môi trường, nhánh, quy ước Jira/commit/MR, stack).
---

# Onboard dự án

## Mục tiêu

Tạo file hồ sơ dự án `CLAUDE.local.md` ở root repo. Mọi skill khác đều đọc file này để biết quy tắc chung và quy ước riêng của dự án.

**Nguyên tắc cốt lõi:** chỉ ghi vào hồ sơ những gì đã đọc thấy trong repo hoặc đã được người dùng xác nhận. Chỗ nào đang suy đoán thì ghi rõ `CHƯA XÁC NHẬN` và hỏi lại người dùng, không tự điền.

## Quy trình

### 1. Kiểm tra hồ sơ cũ

- Nếu đã có `CLAUDE.local.md`: đọc file, tóm tắt cho người dùng, rồi hỏi muốn cập nhật phần nào. Chỉ sửa đúng phần đó, giữ nguyên các phần khác, nhất là mục "Nghiệp vụ đã học" và "Quyền đã được cấp".
- Nếu chưa có: làm tiếp bước 2.

### 2. Tự tìm hiểu repo

Ở bước này chỉ đọc, không sửa gì trong repo.

| Cần biết | Xem ở đâu |
|---|---|
| Framework, TypeScript, thư viện state, thư viện UI, form, data fetching | `package.json` (dependencies) |
| Lệnh lint / type-check / test / build | `package.json` (scripts), file config ESLint/Prettier/Jest/Vitest |
| Nơi khai báo route (để map màn hình ↔ đường dẫn) | Tìm `createBrowserRouter`, `<Route`, `routes`, hoặc thư mục `pages/`, `app/` |
| Tầng gọi API | Tìm instance `axios.create`, `fetch` wrapper, thư mục `services/`, `api/`, các hook RTK Query / React Query |
| Component dùng chung, design token | Thư mục `components/`, `shared/`, `common/`, theme |
| Các nhánh chính | `git branch -a`, `git log --oneline --graph -30 --all` |
| Các môi trường | Tên các file `.env.*`, cấu hình CI (`.gitlab-ci.yml`). Chỉ đọc tên file và tên biến, **không đọc và không chép giá trị** (có thể chứa secret). |

Khi xem lịch sử git, để ý các commit trùng nội dung nằm rải rác trên nhiều nhánh. Đó là dấu hiệu dự án đang dùng chiến lược cherry-pick.

### 3. Hỏi người dùng

Trình bày những gì đã tìm thấy ở bước 2, kèm phán đoán của mình. Sau đó hỏi các mục dưới đây. Các mục này **khác nhau theo từng dự án**, nên luôn phải hỏi, không được lấy từ dự án khác:

1. **Môi trường ↔ nhánh chính:** có những môi trường nào, thứ tự từ thấp lên cao, mỗi môi trường ứng với nhánh nào.
2. **Chiến lược git:**
   - *Chuẩn:* checkout từ nhánh môi trường cao nhất, tạo merge request lần lượt vào các nhánh kiểm thử, cuối cùng là merge request vào nhánh cao nhất để golive.
   - *Cherry-pick:* mỗi môi trường một nhánh tính năng riêng.
3. **Quy ước Jira của PM:** cấu trúc (Epic/Story/Task/Sub-task), cách đặt tên task, ngôn ngữ viết task, các trường bắt buộc, estimate, label, component.
4. **Quy ước git:** cách đặt tên nhánh, format commit message, mẫu mô tả merge request. Commit và mô tả merge request luôn viết tiếng Anh.
5. **Kiểm tra trước khi push:** có bắt buộc chạy lint, type-check, unit test không, và chạy bằng lệnh nào.
6. **Bối cảnh chung:** dự án làm gì, người dùng cuối là ai.

Người dùng chưa biết câu nào thì ghi `CHƯA XÁC NHẬN` vào mục đó, hỏi lại khi cần đến.

### 4. Ghi hồ sơ

- Soạn nội dung theo đúng mẫu ở [ho-so-du-an-template.md](ho-so-du-an-template.md). Phần "Quy tắc chung" trong mẫu phải chép nguyên văn.
- Cho người dùng xem bản nháp. Chỉ ghi ra `CLAUDE.local.md` sau khi người dùng đồng ý.

### 5. Không để lộ lên repo công ty

- Đọc file `.git/info/exclude`.
- Nếu trong đó còn thiếu dòng nào trong 3 dòng `CLAUDE.local.md`, `.claude/skills/`, `.claude/features/`, hỏi người dùng có cho thêm vào không. Được đồng ý mới thêm.
- Sau đó chạy `git status` để chứng minh các file này không còn hiện ra là file mới.
- Riêng `.claude/skills/`: nếu repo đã commit sẵn thư mục này từ trước thì không exclude được cả thư mục. Báo cho người dùng biết và hỏi nên xử lý thế nào.

## Lỗi thường gặp

| Sai | Đúng |
|---|---|
| Thấy có nhánh `develop` liền tự ghi đó là môi trường kiểm thử | Ghi là phán đoán và hỏi xác nhận |
| Chép quy ước của dự án trước sang | Quy ước mỗi dự án mỗi khác, luôn hỏi lại |
| Đọc file `.env` và chép giá trị vào hồ sơ | Chỉ ghi tên môi trường và tên biến |
| Tự ghi file hoặc tự sửa `.git/info/exclude` | Cho xem bản nháp, hỏi xong mới ghi |
