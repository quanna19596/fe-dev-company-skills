# Hồ sơ dự án: <Tên dự án>

> File này chỉ có ở máy cá nhân (đã thêm vào `.git/info/exclude`), không push lên repo.
> Claude đọc file này ở đầu mỗi phiên làm việc.

## Quy tắc chung (bắt buộc, áp dụng cho mọi việc)

- Giao tiếp bằng tiếng Việt. Commit message và mô tả merge request viết bằng tiếng Anh.
- **Hỏi trước mọi hành động** làm thay đổi trạng thái: tạo/chuyển nhánh, commit, push, cherry-pick, rebase, tạo merge request, nội dung đưa lên Jira. Chỉ được tự làm những việc có ghi trong mục "Quyền đã được cấp".
- Không kết nối Jira, GitLab hay bất kỳ công cụ nội bộ nào của công ty. Khi cần làm gì trên các công cụ đó, soạn sẵn nội dung để người dùng tự paste. Việc gì không làm trực tiếp được thì nói rõ để người dùng làm thay.
- Cần làm rõ điều gì với BA, Designer, BE hay Tester thì hỏi thẳng người dùng, coi họ như chính người đó. Ghi rõ câu hỏi dành cho ai.
- Code cũ là legacy, không có tài liệu: **hạn chế tối đa sửa code cũ**.
  - Nếu buộc phải sửa: liệt kê mọi nơi đang dùng chỗ đó và các rủi ro có thể xảy ra, rồi soạn câu hỏi cho BA.
  - BA đồng ý mới sửa. BA không chắc chắn hoặc không đồng ý thì tìm phương án khác.
- Không tạo bản trùng lặp (ví dụ 2 component Button giống hệt nhau).
  - Trước khi viết mới: tìm thứ đã có, đọc và đánh giá nó.
  - Chỉ cần sửa nhẹ mà không ảnh hưởng nơi khác đang dùng thì tái sử dụng.
  - Khác quá nhiều thì viết mới.
- Mỗi tính năng có một ghi chú tại `.claude/features/<mã>.md`. Khi tiếp tục làm một tính năng, đọc ghi chú đó trước. Có quyết định, câu trả lời hay commit mới thì ghi thêm vào.
- Code lệch nhỏ so với Figma (ví dụ bo góc 4px trong code, 6px trong thiết kế): không tự sửa theo Figma. Hỏi Designer, vì có thể design system mới đúng còn Figma nhầm.

## Thông tin chung

- Dự án làm gì:
- Người dùng cuối:

## Môi trường & nhánh

| Thứ tự (thấp → cao) | Môi trường | Nhánh chính |
|---|---|---|
| 1 | | |

## Chiến lược git

<!-- Chọn một trong hai, xoá cái còn lại -->

**Chuẩn:** checkout nhánh tính năng từ `<nhánh cao nhất>` → MR vào `<nhánh kiểm thử 1>` → … → khi golive, MR vào `<nhánh cao nhất>`.

**Cherry-pick:** với các môi trường A → B → C (C là production) thì có các nhánh tính năng A1, B1, C1 checkout lần lượt từ nhánh của từng môi trường.
1. Code và push lên A1, tạo merge request vào A.
2. Khi lên B: checkout B1 và cherry-pick các commit từ A1 (rebase nếu cần), tạo merge request vào B.
3. Khi golive: checkout C1 và cherry-pick từ B1, tạo merge request vào C.

## Quy ước Jira (theo PM)

- Cấu trúc:
- Đặt tên task:
- Ngôn ngữ viết task:
- Trường bắt buộc:

## Quy ước git

- Tên nhánh:
- Commit message (tiếng Anh):
- Mẫu mô tả merge request (tiếng Anh):

## Stack & kiểm tra

- Ngôn ngữ: TypeScript / JavaScript
- State:
- UI:
- Lệnh lint / type-check / test (bắt buộc trước khi push?):

## Bản đồ code

- Khai báo route:
- Tầng gọi API (instance, baseURL nằm ở đâu):
- Component dùng chung:
- Design token / theme:

## Nghiệp vụ đã học

<!-- Ghi thêm dần trong quá trình làm. Mỗi dòng ghi ngày và nguồn (BA xác nhận / đọc code). -->

## Quyền đã được cấp

<!-- Các việc người dùng đã cho phép Claude tự làm, không cần hỏi. Ban đầu để trống. -->
