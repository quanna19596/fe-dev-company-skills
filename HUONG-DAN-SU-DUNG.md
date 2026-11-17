# Hướng dẫn sử dụng bộ skill (luồng công ty)

Đọc từ trên xuống trong lần đầu dùng. Những lần sau chỉ cần xem mục "Tra nhanh" ở cuối.

---

## 0. Trước khi bắt đầu

- [ ] **Quy định công ty.** Claude Code gửi code của dự án và mọi nội dung bạn paste vào (BRD, BE Specs, bug) lên máy chủ của Anthropic để xử lý. Hãy chắc chắn công ty cho phép dùng Claude Code trên repo này.
- [ ] **Claude Code đã cài và đăng nhập** trên máy công ty (app desktop hoặc CLI `claude`).
- [ ] **Đã lấy được bộ skill** từ git cá nhân về máy công ty.
- [ ] **(Tuỳ chọn) Figma MCP:** xem cách kết nối trong `README.md`. Chưa kết nối cũng dùng được, khi đó bạn gửi ảnh chụp màn hình và thông số copy từ tab Inspect.

---

## 1. Cài vào dự án (một lần cho mỗi dự án)

### 1.1 Copy skill

```bash
mkdir -p <thư-mục-dự-án>/.claude
```

```bash
cp -R <nơi-clone>/fe-dev-company-skills/.claude/skills <thư-mục-dự-án>/.claude/
```

Nếu dự án đã có sẵn `.claude/skills/`, copy từng thư mục skill vào, không ghi đè cả thư mục.

### 1.2 Mở Claude Code tại root dự án

- **App desktop:** tạo phiên mới và chọn thư mục dự án.
- **Terminal:** `cd <thư-mục-dự-án>` rồi gõ `claude`.

Kiểm tra: gõ `/` vào ô chat. Danh sách gợi ý phải có đủ 7 skill: `onboard-project`, `analyze-requirements`, `plan-jira-tasks`, `locate-and-assess`, `implement-feature`, `fix-bug`, `write-report`.

### 1.3 Tạo hồ sơ dự án

Gõ:

```
/onboard-project
```

Claude sẽ làm lần lượt:
1. **Đọc repo** (chỉ đọc): `package.json`, router, tầng gọi API, component dùng chung, các nhánh git, tên các file `.env.*` (không đọc giá trị bên trong).
2. **Trình bày những gì tìm được** và hỏi bạn:
   - môi trường ↔ nhánh;
   - ghi chú về git (ghi nguyên văn lời bạn);
   - quy ước Jira của PM;
   - quy ước nhánh, commit, MR;
   - lệnh lint, test;
   - bối cảnh dự án.

   Câu nào chưa biết, cứ trả lời *"chưa rõ"*. Claude ghi `CHƯA XÁC NHẬN` vào mục đó và hỏi lại khi cần đến.
3. **Đưa bản nháp `CLAUDE.local.md`.** Bạn đọc, sửa nếu cần, rồi đồng ý cho ghi.
4. **Xin thêm 3 dòng vào `.git/info/exclude`:** `CLAUDE.local.md`, `.claude/skills/`, `.claude/features/`. Bạn đồng ý thì Claude thêm, rồi chạy `git status` để chứng minh các file này không hiện ra nữa.

✅ **Xong khi:** đã có `CLAUDE.local.md` ở root, và `git status` không còn hiện `.claude/` hay `CLAUDE.local.md`.

> Sau này dự án thay đổi (thêm môi trường, PM đổi quy ước…) thì gọi lại `/onboard-project` và nói phần cần cập nhật.

---

## 2. Làm tính năng mới

**Nên mở một phiên riêng cho mỗi tính năng.** Đặt tên phiên theo mã ticket để dễ tìm lại.

### Bước 2.1: Phân tích yêu cầu

```
/analyze-requirements
```

Bạn đưa vào:
- **Mã ticket** (hoặc một tên ngắn nếu chưa có ticket).
- **BRD:** paste phần liên quan tới tính năng.
- **BE Specs:** paste phần liên quan.
- **Figma:**
  - đã nối MCP: dán link tới từng frame;
  - chưa nối: dán ảnh chụp từng màn hình và từng trạng thái, kèm thông số copy từ tab Inspect.

Bạn nhận về:
- tóm tắt (nguyên văn từ BRD);
- danh sách màn hình;
- bảng mapping API;
- các chỗ mâu thuẫn hoặc còn thiếu;
- **câu hỏi chia theo người: BA, Designer, BE.**

Tiếp theo:
1. Đồng ý cho Claude lưu kết quả vào `.claude/features/<mã>.md`.
2. Copy từng câu hỏi gửi đúng người. Mỗi câu đã tự đủ bối cảnh, người nhận đọc là hiểu.
3. Khi có câu trả lời, paste lại cho Claude, ví dụ:
   ```
   BA trả lời câu 2: chỉ admin mới được thấy nút Huỷ.
   Designer trả lời câu 1: giữ bo góc 4px theo design system.
   ```
   Claude cập nhật vào ghi chú tính năng.

### Bước 2.2: Chia task Jira

```
/plan-jira-tasks
```

1. Lần đầu dùng với dự án, Claude có thể hỏi quy ước Jira nếu hồ sơ chưa có.
2. Claude đưa danh sách tên task để bạn chỉnh cách chia.
3. Bạn đồng ý, Claude soạn từng task trong một khối riêng. **Bạn copy và tự tạo task trên Jira.**
4. Báo lại các mã task cho Claude, ví dụ: *"đã tạo ABC-101, ABC-102, ABC-103"*.

### Bước 2.3: Tạo nhánh (bạn tự làm)

Bộ skill hiện chưa có skill cho git. Bạn tự tạo nhánh như bình thường.

Nếu muốn, báo cho Claude biết nhánh và nhánh gốc, ví dụ: *"mình tạo nhánh feature/ABC-101 từ develop"*. Claude ghi vào ghi chú tính năng, sau này `write-report` sẽ dùng.

### Bước 2.4: Khoanh vùng và đánh giá

```
/locate-and-assess
```

Claude tìm điểm bắt đầu trong code (theo route, text trên UI, endpoint), rồi kiểm kê những thứ có thể tái sử dụng. Mỗi thứ được phân loại:
- **Dùng nguyên.**
- **Sửa nhẹ an toàn:** chỉ thêm prop tuỳ chọn, không ảnh hưởng nơi khác đang dùng.
- **Sửa code cũ:** cần BA duyệt. Claude soạn sẵn câu hỏi, gồm cả rủi ro và phương án thay thế.
- **Viết mới.**

Bạn:
1. Đọc kế hoạch triển khai.
2. Gửi câu hỏi cho BA hoặc Designer (nếu có), rồi mang câu trả lời về.
3. **Duyệt kế hoạch.** Chưa duyệt thì Claude không code.

### Bước 2.5: Code

```
/implement-feature
```

Claude:
- kiểm tra đủ điều kiện: có hồ sơ dự án, có phân tích yêu cầu, kế hoạch đã duyệt, đang đứng đúng nhánh;
- code theo khuôn mẫu sẵn có của dự án, chỉ sửa những chỗ đã duyệt;
- chạy lint, type-check, test theo hồ sơ dự án;
- tổng kết: file tạo mới, file đã sửa, phần còn chờ câu trả lời, kết quả kiểm tra.

Bạn: review code, chạy thử trên máy.

### Bước 2.6: Commit, push, merge request (bạn tự làm)

- Claude không commit, không push.
- Muốn Claude soạn commit message (tiếng Anh, theo quy ước dự án), gõ: *"soạn commit message cho thay đổi này"*.
- Muốn soạn mô tả merge request, gõ: *"soạn mô tả MR"*.

### Bước 2.7: Viết báo cáo (khi xong tính năng)

```
/write-report
```

1. Claude hỏi nhánh nào chứa đầy đủ code, và lấy diff so với mốc nào (nhánh gốc hoặc commit gốc).
2. Claude đưa báo cáo theo đúng format:
   ```
   <Tóm tắt nguyên văn từ BRD>

   Chi tiết thay đổi:

   * Màn hình Chi tiết đơn hàng (/orders/:id):
      * Call API POST /api/v1/orders/{id}/cancel để huỷ đơn hàng
   * Giao diện màn hình (/orders)
   ```
3. **Kiểm tra các dòng đánh dấu `(cần xác nhận)`.** Đó là những tác dụng API Claude suy ra từ code vì không có trong BE Specs.

---

## 3. Fix bug do tester log

```
/fix-bug
```

1. **Paste nội dung bug** từ Jira: mã bug, môi trường, các bước tái hiện, hiện trạng, mong muốn. Có ảnh hoặc video thì dán luôn.
2. **Báo nhánh cần sửa.** Claude sẽ hỏi.
3. **Claude phân loại bug trước khi sửa:**

   | Trường hợp | Claude làm gì |
   |---|---|
   | Code sai so với tài liệu | Sửa |
   | Mong muốn của tester khác với BRD | Không sửa, soạn câu hỏi cho BA |
   | Lỗi nằm trong code cũ, đã có từ trước | Không sửa, soạn câu trả lời cho tester và câu hỏi cho BA |
   | Response API sai so với BE Specs | Không vá ở FE, soạn câu hỏi cho BE kèm bằng chứng |
   | Tài liệu không nói rõ | Hỏi trước khi sửa |

4. **Nếu là bug của tính năng:** Claude tìm nguyên nhân gốc và nêu bằng chứng, rồi sửa với phạm vi nhỏ nhất, chạy kiểm tra, và giải thích cho bạn.
   - Cần thêm thông tin thì Claude sẽ xin, ví dụ response API thật, log trên console.
5. **Bạn tự commit và push.** Muốn thì nhờ Claude soạn commit message, hoặc comment trả lời tester trên Jira.

> Nếu bug thuộc tính năng đã có ghi chú, Claude đọc ghi chú đó nên hiểu ngay bối cảnh. Nếu bug thuộc phần không có ghi chú, Claude sẽ hỏi thêm.

---

## 4. Hôm sau làm tiếp

- Mở phiên mới tại root dự án và gõ:
  ```
  tiếp tục tính năng ABC-101
  ```
  Claude đọc `.claude/features/ABC-101.md` (tài liệu, câu hỏi, kế hoạch, nhánh), nên không cần paste lại.
- Đang trong một phiên mà chuyển sang việc khác hẳn: gõ `/clear` trước, để Claude không bị lẫn bối cảnh cũ.
- Muốn biết tính năng đang tới đâu: *"tóm tắt trạng thái ABC-101"*.

---

## 5. Chọn model và mức effort

Đổi model và effort ở ô chọn model trong app. Nếu dùng terminal thì gõ `/model`.

### Theo từng bước

| Bước | Model | Effort |
|---|---|---|
| `onboard-project` | Sonnet | medium |
| `analyze-requirements` | Opus | high |
| `plan-jira-tasks` | Sonnet | medium |
| `locate-and-assess` | Opus | high |
| `implement-feature`: UI và tích hợp API thông thường | Sonnet | high |
| `implement-feature`: logic phức tạp, đụng nhiều code cũ | Opus | high |
| `fix-bug` | Opus | high |
| `write-report` | Sonnet | medium |

### Khi nào nâng effort hoặc đổi model

Nâng từng nấc một. Trước khi nâng, kiểm tra Claude đã có **đủ thông tin** chưa (BRD, response API thật, log, các bước tái hiện…). Phần lớn trường hợp Claude làm sai là do thiếu thông tin, không phải do model yếu.

| Mức | Dùng khi | Ví dụ |
|---|---|---|
| **Opus, high** | Mặc định cho việc cần suy luận | Phân tích BRD, đánh giá tái sử dụng, bug thông thường |
| **Opus, xhigh** | Đã dùng high mà kết quả chưa đạt, hoặc việc vốn rất khó | Bug liên quan nhiều tầng: race condition, state lệch giữa nhiều component, chỉ lỗi ở một môi trường. Đánh giá ảnh hưởng khi phải sửa component dùng ở hàng chục nơi. Lần theo một module legacy rối và lớn |
| **Opus, max** | Hiếm khi dùng: xhigh vẫn không ra, và việc đủ quan trọng để chấp nhận chậm và tốn | Bug nghiêm trọng sát ngày golive mà chưa tìm ra nguyên nhân |
| **Fable 5.2** | Opus đã thử ở mức cao mà vẫn bế tắc với cùng một vấn đề. Hoặc việc dài, nhiều bước, cần suy luận sâu nhất | Gỡ một bug mà Opus xhigh đã thử nhiều lần không ra. Sau này, ở luồng freelance: thiết kế kiến trúc cả hệ thống từ đầu |

**Về Fable 5.2:**
- Đây là model mạnh nhất đang có, giá khoảng **gấp đôi Opus 5.5**, và mỗi lượt trả lời có thể chạy tới vài phút.
- Khi dùng Fable, bắt đầu ở effort **high**. Chỉ nâng lên xhigh khi thấy rõ là có ích. Effort max hầu như không cần.
- Không dùng Fable cho việc hằng ngày như chia task hay viết báo cáo: tốn mà không tốt hơn bao nhiêu.

---

## 6. Ghi lại phản hồi để cải tiến skill

Mỗi khi skill làm chưa đúng ý, ghi lại một dòng theo mẫu (ví dụ ghi vào note riêng):

```
[ngày] [skill] Tình huống: ... | Claude đã làm: ... | Mong muốn: ...
```

Ví dụ:
```
[18/11] [plan-jira-tasks] Tình huống: chia task tính năng huỷ đơn | Claude đã làm: tách 6 task quá nhỏ | Mong muốn: PM muốn mỗi màn hình 1 task
```

Sau đó mở Claude Code trong repo `fe-dev-company-skills` (Claude sẽ đọc `CLAUDE.md` để nắm ngữ cảnh), paste các dòng này vào và nhờ sửa skill.

---

## 7. Xử lý sự cố

| Vấn đề | Cách xử lý |
|---|---|
| Gõ `/` không thấy skill | Kiểm tra skill nằm đúng ở `<dự án>/.claude/skills/<tên>/SKILL.md`, và Claude Code đang mở ở root dự án. Khởi động lại phiên |
| Claude không tự chọn skill khi bạn mô tả việc | Gọi thẳng `/tên-skill` |
| Claude không theo quy tắc chung (tự commit, tự sửa code cũ…) | Nhắc Claude: *"đọc CLAUDE.local.md và làm theo Quy tắc chung"*. Ghi lại vào phản hồi ở mục 6 |
| Claude hỏi lại những thứ đã trả lời từ trước | Kiểm tra câu trả lời đã được ghi vào `CLAUDE.local.md` hoặc ghi chú tính năng chưa. Nếu chưa, bảo Claude ghi vào |
| `git status` vẫn hiện `.claude/skills/` | Có thể repo đã commit sẵn thư mục `.claude/` từ trước. Hỏi Claude cách xử lý, không tự xoá gì |
| Phiên quá dài, Claude bắt đầu quên hoặc lẫn | `/clear` rồi gõ *"tiếp tục tính năng <mã>"* |

---

## Tra nhanh

| Tình huống | Gõ |
|---|---|
| Dự án mới, hoặc chưa có `CLAUDE.local.md` | `/onboard-project` |
| Nhận BRD, Figma, BE Specs | `/analyze-requirements` |
| Chia task Jira | `/plan-jira-tasks` |
| Tìm chỗ code, đánh giá tái sử dụng | `/locate-and-assess` |
| Code | `/implement-feature` |
| Tester log bug | `/fix-bug` |
| Xong tính năng, viết báo cáo | `/write-report` |
| Làm tiếp hôm trước | `tiếp tục tính năng <mã>` |
| Có câu trả lời của BA, Designer, BE | `BA trả lời câu <n>: ...` |
| Nhờ soạn commit message hoặc mô tả MR | `soạn commit message` / `soạn mô tả MR` |
