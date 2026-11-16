# Ngữ cảnh để phát triển bộ skill này

> File này dành cho các phiên Claude Code mở trong chính repo này, để tiếp tục hoàn thiện bộ skill trên bất kỳ máy nào.
> Khi copy bộ skill vào dự án công ty thì **chỉ copy `.claude/skills/`**, không copy file này.
> Mỗi khi có quyết định mới, cập nhật file này rồi commit và push.

## Người dùng

- Giao tiếp bằng tiếng Việt. Nội dung skill viết tiếng Việt.
- Làm 2 luồng việc:
  - **Ở công ty:** Frontend Web Developer, ReactJS, thường dùng TypeScript.
  - **Ngoài giờ:** nhận dự án trực tiếp từ khách hàng và tự lo mọi việc. Luồng này **chưa bắt đầu xây skill**, sẽ làm sau khi xong luồng công ty.
- Mục tiêu: có một hệ thống skill để Claude làm việc thay người dùng. Người dùng gọi skill bằng `/tên`, hoặc Claude tự chọn đúng skill.

## Quy trình ở công ty (bộ skill này mô phỏng)

1. Nhận tài liệu: BRD (từ BA), Figma (từ Designer), BE Specs (từ BE).
2. Chia task trên Jira.
3. Checkout nhánh.
4. Khoanh vùng code.
5. Code.
6. Push và tạo merge request vào nhánh môi trường kiểm thử. Việc deploy do đội vận hành lo; deploy xong thì người dùng báo tester.
7. Tester log bug trên Jira. Người dùng fix ngay trên nhánh tính năng rồi push lại. Lặp lại tới khi hết bug.
8. Tạo merge request lên môi trường kiểm thử cao hơn, hoặc vào production chờ lịch golive.
9. Viết báo cáo cho tính năng.

Với dự án mới tham gia, phải tìm hiểu trước: các môi trường ↔ nhánh chính, bối cảnh chung. Nghiệp vụ thì học dần trong quá trình làm.

## Quy tắc người dùng đã đặt

- **Không kết nối MCP tới Jira, GitLab hay công cụ nội bộ** (quy định công ty). Claude xuất nội dung để người dùng tự paste. Việc gì không làm được trực tiếp thì nói rõ để người dùng làm thay. BRD và BE Specs do người dùng paste vào.
  - **Ngoại lệ:** Figma MCP. Người dùng muốn kết nối. Khi chưa kết nối được thì dùng ảnh chụp màn hình và thông số từ tab Inspect.
- **Hỏi trước MỌI hành động**: tạo nhánh, commit, push, merge request, nội dung đưa lên Jira… Người dùng sẽ tự nới quyền cho từng việc cụ thể khi đủ tin tưởng.
- Cần hỏi BA, Designer hay Tester thì hỏi thẳng người dùng, coi người dùng như chính người đó. Người dùng sẽ tự đi hỏi và mang câu trả lời về.
- Commit message và mô tả merge request viết **tiếng Anh**.
- Quy ước Jira (theo PM), quy ước branch/commit/merge request, thư viện state/UI, có unit test hay lint không: **khác nhau theo từng dự án**. Phải hỏi khi onboard từng dự án, không được đoán.
- **Code legacy** (nhiều năm, không có tài liệu, không hỏi được người viết cũ):
  - Hạn chế tối đa sửa code cũ. Phải sửa thì hỏi BA trước, kèm các rủi ro. BA không đồng ý hoặc không chắc thì tìm phương án khác.
  - Nhưng không tạo bản trùng lặp. Đọc và đánh giá thứ đang có: sửa nhẹ mà không ảnh hưởng nơi khác thì tái sử dụng, khác quá thì viết mới.
- **Code lệch nhỏ với Figma** (ví dụ bo góc 4px trong code, 6px trong Figma): hỏi Designer, không tự sửa. Có thể design system mới đúng còn Figma nhầm.
- **Git:** nhánh gốc tuỳ dự án.
  - *Chuẩn:* checkout từ nhánh môi trường cao nhất → merge request lần lượt xuống các nhánh kiểm thử → merge request vào nhánh cao nhất để golive.
  - *Cherry-pick* (dự án lịch sử commit lộn xộn): với các môi trường A → B → C, có các nhánh tính năng A1, B1, C1. Code và push lên A1; checkout B1 rồi cherry-pick từ A1; khi golive checkout C1 rồi cherry-pick từ B1. Rebase nếu cần.
- **Format báo cáo:**
  ```
  <Tóm tắt lấy nguyên văn từ BRD>
  Chi tiết thay đổi:
  * Màn hình <Tên tiếng Việt theo nghiệp vụ> (<path không có domain>):
     * Call API <METHOD> <url> để <tác dụng>
  * Giao diện màn hình (<path>)      ← màn hình chỉ sửa UI, không có dòng con
  ```

## Thiết kế bộ skill

- **Phân phối:** repo cá nhân này. Khi dùng thì copy `.claude/skills/` vào từng dự án (máy nào cũng được), hoặc vào `~/.claude/skills/`. Vì vậy skill phải tự chạy được, không phụ thuộc cấu hình riêng của máy.
- **Các skill nối nhau qua 2 file chỉ có ở máy cá nhân,** đã thêm vào `.git/info/exclude` của dự án:
  - `CLAUDE.local.md`: hồ sơ dự án, do `onboard-project` tạo từ `onboard-project/ho-so-du-an-template.md`. Phần đầu file chứa các quy tắc chung ở trên.
  - `.claude/features/<mã>.md`: ghi chú của từng tính năng, theo mẫu `analyze-requirements/ghi-chu-tinh-nang-template.md`. Ghi chú lưu **commit gốc** của từng nhánh tính năng. Commit gốc là mốc để lấy diff và danh sách commit cherry-pick, vì sau khi merge thì `origin/<env>..<nhánh>` trả về rỗng.
- **Luồng skill:** onboard-project → analyze-requirements → plan-jira-tasks → start-feature → locate-and-assess → implement-feature → ship-changes → (fix-bug → ship-changes)* → promote-env → write-report.
- **Không dùng superpowers hay plugin quy trình khác.** Các plugin đó ép TDD, brainstorm, worktree, tự merge hay push, nên xung đột với quy trình của người dùng. Phần hữu ích (tìm nguyên nhân gốc trước khi sửa, chạy kiểm tra xong mới báo là xong) đã đưa thẳng vào các skill. Chỉ thêm plugin khi có một việc cụ thể lặp lại mà bộ skill chưa làm được, và phải đọc nội dung plugin trước khi cài.

## Trạng thái & việc tiếp theo

- Đã viết đủ 10 skill cho luồng công ty. **Chưa skill nào được thử trên dự án thật.** Người dùng sẽ thử và cải tiến dần khi dùng thực tế.
- **Câu hỏi còn mở:** dự án cherry-pick mà tester log bug ở môi trường cao hơn (B, C) thì fix trên nhánh nào trước, rồi đưa sang các nhánh còn lại thế nào? Trả lời xong thì cập nhật skill `fix-bug`.
- **Chưa kiểm chứng:** các lệnh kết nối Figma MCP trong README.
- **Tiếp theo:** xây skill cho luồng freelance ngoài giờ.
