# Ngữ cảnh repo `fe-dev-company-skills`

File này dành cho Claude khi người dùng mở repo này để chỉnh sửa hoặc mở rộng bộ skill. File không được copy vào dự án công ty (chỉ thư mục `.claude/skills/` được copy).

## Repo này là gì

Bộ skill Claude Code giúp Claude làm việc thay người dùng, như một bản sao của họ. Người dùng là Frontend Web Developer (ReactJS, thường dùng TypeScript), làm full-time ở công ty. Ngoài giờ họ nhận dự án freelance trực tiếp từ khách hàng.

- **Luồng 1, công ty:** đang làm (thư mục `.claude/skills/`).
- **Luồng 2, freelance ngoài giờ:** chưa bắt đầu, sẽ làm sau.

Cách phân phối: repo nằm ở `~/Workspace/AI/fe-dev-company-skills` trên máy cá nhân, remote `https://github.com/quanna19596/fe-dev-company-skills.git`. Khi cần dùng ở dự án nào, người dùng copy `.claude/skills/` vào root dự án đó (máy nào cũng được). Vì vậy **mọi skill phải tự chứa**: không phụ thuộc `~/.claude`, không phụ thuộc plugin (kể cả superpowers).

## Bối cảnh làm việc ở công ty (để viết skill cho đúng)

- Tài liệu đầu vào: BRD (từ BA), Figma (từ Designer), BE Specs (từ BE). Người dùng chia task trên Jira, code, tạo merge request trên GitLab, tester test và log bug trên Jira, rồi lên môi trường cao hơn hoặc golive.
- Dự án legacy nhiều năm, không có tài liệu, không hỏi được người viết code cũ:
  - Hạn chế tối đa sửa code cũ. Phải sửa thì hỏi BA trước, kèm các rủi ro.
  - Không tạo bản trùng lặp. Đánh giá thứ đã có trước khi viết mới.
- Code lệch nhỏ so với Figma: hỏi Designer, không tự sửa theo Figma.
- Công ty cấm kết nối công cụ ngoài, nên không dùng MCP cho Jira hay GitLab. Claude soạn sẵn nội dung để người dùng tự paste. Riêng **Figma MCP** người dùng chọn dùng.
- Cần hỏi BA, Designer, BE, Tester thì hỏi thẳng người dùng. Họ tự đi hỏi rồi mang câu trả lời về.

## Quy ước khi viết hoặc sửa skill

- Nội dung skill viết **tiếng Việt**. Commit message và mô tả merge request mà skill soạn ra viết **tiếng Anh**.
- Frontmatter `description` bắt đầu bằng "Dùng khi…" và chỉ nêu khi nào dùng, kèm từ khoá người dùng hay nói. Không tóm tắt quy trình trong description.
- Mỗi skill đọc `CLAUDE.local.md` (hồ sơ dự án, do `onboard-project` tạo) và `.claude/features/<mã>.md` (ghi chú tính năng, mẫu ở `analyze-requirements/ghi-chu-tinh-nang-template.md`).
- Phần "Quy tắc chung" nằm trong `onboard-project/ho-so-du-an-template.md`. Đổi quy tắc chung thì sửa ở đó.
- Quy ước nào khác nhau theo dự án (Jira, đặt tên nhánh, commit, MR, thư viện, lint/test) thì skill phải **hỏi người dùng**, không được mặc định.
- Mọi hành động làm thay đổi trạng thái đều phải hỏi người dùng trước.
- Đổi skill thì cập nhật luôn `README.md` và `HUONG-DAN-SU-DUNG.md` cho khớp.
- Có quyết định mới thì ghi vào bảng "Các quyết định đã chốt" bên dưới. Commit và push chỉ khi người dùng đồng ý.

## Các quyết định đã chốt

| Ngày | Quyết định |
|---|---|
| 2026-11-14 | Bối cảnh dự án công ty (để hiểu, không phải quy trình bắt buộc): với các môi trường A → B → C (C là production), có dự án checkout nhánh tính năng từ nhánh cao nhất rồi tạo merge request lần lượt vào các nhánh; có dự án lịch sử commit lộn xộn thì mỗi môi trường một nhánh tính năng A1, B1, C1, code ở A1 rồi cherry-pick sang B1, C1. Thực tế còn tuỳ tình hình từng lúc |
| 2026-11-16 | Hồ sơ dự án là `CLAUDE.local.md`. Exclude `CLAUDE.local.md`, `.claude/skills/`, `.claude/features/` qua `.git/info/exclude` |
| 2026-11-16 | Báo cáo theo format: tóm tắt BRD, rồi "Chi tiết thay đổi". Màn hình có API: `* Màn hình <Tên> (<path>):` kèm các dòng con `* Call API <METHOD> <url> để <tác dụng>`. Màn hình chỉ sửa UI: một dòng `* Giao diện màn hình (<path>)` |
| 2026-11-17 | **Đã xoá** các skill `start-feature`, `ship-changes`, `promote-env`. Quy trình git thay đổi theo tình hình từng dự án, người dùng chưa khái quát được. Không làm lại skill git cho tới khi người dùng đề nghị. Các skill hiện có không tự commit, push, tạo nhánh |
| 2026-11-17 | Không dùng superpowers hay plugin ngoài cho bộ skill này |

## Việc còn mở

- Người dùng review chi tiết 7 skill hiện có.
- Thử trên dự án thật (bắt đầu từ 2026-11-18), rồi sửa skill theo phản hồi thực tế.
- Skill git: làm sau, khi người dùng đã khái quát được quy trình.
- Luồng freelance: chưa bắt đầu.

## Cấu trúc

```
README.md                  Tổng quan + cài đặt nhanh
HUONG-DAN-SU-DUNG.md       Hướng dẫn sử dụng chi tiết từng bước
CLAUDE.md                  File này
.claude/skills/
  onboard-project/         + ho-so-du-an-template.md (mẫu CLAUDE.local.md, chứa Quy tắc chung)
  analyze-requirements/    + ghi-chu-tinh-nang-template.md (mẫu ghi chú tính năng)
  plan-jira-tasks/
  locate-and-assess/
  implement-feature/
  fix-bug/
  write-report/
```
