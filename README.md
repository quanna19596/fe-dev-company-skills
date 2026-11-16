# Bộ skill Claude Code — Frontend (luồng công ty)

Bộ skill để Claude Code làm việc theo đúng quy trình Frontend React ở công ty: từ tìm hiểu dự án, phân tích tài liệu, chia task, code, ship cho tới fix bug và viết báo cáo.

## Cài vào một dự án

1. Copy thư mục `.claude/skills/` của repo này vào root của dự án.
   - Nếu dự án đã có sẵn `.claude/skills/`, copy từng thư mục skill vào, đừng ghi đè cả thư mục.
2. Để không push bộ skill, hồ sơ dự án và ghi chú tính năng lên repo công ty, thêm 3 dòng sau vào file `.git/info/exclude` của dự án. File này chỉ có ở máy bạn, không được commit.

   ```
   .claude/skills/
   .claude/features/
   CLAUDE.local.md
   ```

   `.claude/features/` là nơi các skill lưu ghi chú cho từng tính năng: nội dung BRD/BE Specs bạn đã paste, kết quả phân tích, câu hỏi và câu trả lời, nhánh, commit, bug. Nhờ ghi chú này, phiên làm việc sau vẫn nối tiếp được, không phải paste lại tài liệu.

   Nếu muốn, có thể để skill `onboard-project` tự làm bước này (nó sẽ hỏi bạn trước).
3. Mở Claude Code ở root dự án và gọi `/onboard-project`. Skill này tạo ra hồ sơ dự án `CLAUDE.local.md`, mọi skill khác đều dựa vào file đó.

> Cách khác: copy vào `~/.claude/skills/` thì máy đó dùng được bộ skill cho mọi dự án, không phải copy cho từng dự án. Dù vậy, mỗi dự án vẫn cần chạy `onboard-project` để có hồ sơ riêng.

## Kết nối Figma MCP (tuỳ chọn)

Làm một lần trên mỗi máy. Có 2 cách:

- **Remote server của Figma** (đăng nhập bằng tài khoản Figma):
  ```bash
  claude mcp add --transport http figma https://mcp.figma.com/mcp
  ```
  Sau đó mở Claude Code, gõ `/mcp` và chọn `figma` để đăng nhập.
- **Desktop server** (cần app Figma desktop, bật Dev Mode MCP server trong Preferences):
  ```bash
  claude mcp add --transport http figma-desktop http://127.0.0.1:3845/mcp
  ```

Hai lệnh trên ghi theo tài liệu Figma tại thời điểm viết. Nếu chạy không được, hãy đối chiếu lại tài liệu hiện tại của Figma.

Nếu chưa kết nối được Figma MCP, các skill sẽ xin bạn ảnh chụp màn hình thiết kế và thông số copy từ tab Inspect.

## Danh sách skill

| Skill | Dùng khi | Trạng thái |
|---|---|---|
| `onboard-project` | Bắt đầu với một dự án mới, hoặc dự án chưa có `CLAUDE.local.md` | ✅ Đã có |
| `write-report` | Viết báo cáo sau khi làm xong tính năng | ✅ Đã có |
| `analyze-requirements` | Nhận BRD, Figma, BE Specs cho tính năng mới | ✅ Đã có |
| `plan-jira-tasks` | Chia task để paste vào Jira | ✅ Đã có |
| `start-feature` | Tạo nhánh tính năng | ✅ Đã có |
| `locate-and-assess` | Tìm điểm bắt đầu trong code, đánh giá có tái sử dụng được không | ✅ Đã có |
| `implement-feature` | Code tính năng | ✅ Đã có |
| `ship-changes` | Commit, push, soạn merge request | ✅ Đã có |
| `fix-bug` | Fix bug do tester log trên Jira | ✅ Đã có |
| `promote-env` | Đưa code lên môi trường kiểm thử cao hơn hoặc production | ✅ Đã có |

## Luồng làm việc

```
onboard-project  (một lần cho mỗi dự án)
      ↓
analyze-requirements → plan-jira-tasks → start-feature → locate-and-assess → implement-feature → ship-changes
                                                                                                     ↓
                                              tester log bug → fix-bug → ship-changes  (lặp lại tới khi hết bug)
                                                                                                     ↓
                                                         promote-env  (lên môi trường cao hơn / golive) → write-report
```

Các skill nối với nhau qua 2 file chỉ có ở máy bạn, không push lên repo:
- `CLAUDE.local.md`: hồ sơ dự án.
- `.claude/features/<mã>.md`: ghi chú của từng tính năng.

Gọi trực tiếp bằng `/tên-skill`, hoặc mô tả việc cần làm để Claude tự chọn skill phù hợp.
