---
name: analyze-requirements
description: Dùng khi người dùng nhận tính năng mới và đưa vào tài liệu BRD, BE Specs, link hoặc ảnh Figma; khi nói "phân tích yêu cầu", "đọc BRD", "đọc tài liệu", "có tính năng mới"; hoặc khi cần đối chiếu BRD, Figma và BE Specs để tìm chỗ mâu thuẫn hay còn thiếu trước khi chia task và code.
---

# Phân tích yêu cầu

## Mục tiêu

Hiểu chính xác tính năng cần làm từ 3 nguồn: BRD (từ BA), Figma (từ Designer), BE Specs (từ BE). Tìm ra mọi chỗ mâu thuẫn hoặc còn thiếu giữa 3 nguồn. Kết quả lưu vào ghi chú tính năng để các skill sau dùng tiếp.

**Nguyên tắc cốt lõi:** tài liệu không nói rõ thì không tự quyết. Mọi khoảng trống đều trở thành câu hỏi gửi đúng người.

## Chuẩn bị

1. Đọc `CLAUDE.local.md`. Nếu repo chưa có file này, dùng skill `onboard-project` trước.
2. Hỏi mã ticket Jira (nếu đã có) hoặc một tên ngắn cho tính năng.
3. Nếu `.claude/features/<mã>.md` đã tồn tại thì đọc nó và làm tiếp, không bắt đầu lại từ đầu.

## Thu thập tài liệu

Người dùng không kết nối Jira hay Confluence, nên mọi tài liệu phải do người dùng paste vào.

- **BRD và BE Specs:** xin người dùng paste vào. Thiếu phần nào thì xin đúng phần đó.
- **Figma:**
  - *Có tool Figma MCP* (tên tool chứa `figma`): xin link tới từng frame hoặc màn hình liên quan, rồi dùng tool để lấy cấu trúc, thông số, biến (design token) và ảnh chụp.
  - *Không có tool Figma MCP:* xin ảnh chụp từng màn hình và từng trạng thái, kèm thông số copy từ tab Inspect của Dev Mode cho các phần tử quan trọng.
  - Không đoán thông số thiết kế từ ảnh khi người dùng có thể cung cấp số liệu chính xác.

## Phân tích

Đối chiếu 3 nguồn và trả lời các câu sau. Câu nào không có câu trả lời trong tài liệu thì đưa vào danh sách câu hỏi.

1. **Phạm vi:** tính năng gồm những màn hình nào? Màn hình nào làm mới, màn hình nào sửa?
2. **Hành vi:** mỗi hành động của người dùng dẫn tới điều gì?
   - Điều hướng sang đâu?
   - Có gọi API không, gọi API nào?
   - Có hiện thông báo gì không?
3. **Trạng thái của màn hình:** loading, rỗng, lỗi, không có quyền. Figma có vẽ đủ các trạng thái này không?
4. **Dữ liệu và validation:** trường nào bắt buộc, giới hạn độ dài và định dạng, câu thông báo lỗi.
5. **Phân quyền:** vai trò nào được thấy hoặc thao tác chức năng này?
6. **Mapping API:** mỗi dữ liệu hiển thị lấy từ field nào trong response. Mỗi field nhập vào gửi qua field nào trong request.
   - Kiểm tra BE Specs có đủ field mà Figma và BRD cần không.
   - Kiểm tra kiểu dữ liệu, enum, phân trang, sắp xếp, lọc.
7. **Mâu thuẫn giữa các nguồn:** BRD nói một đằng, Figma vẽ một nẻo. Figma có field mà BE không trả về. Các thông báo lỗi không khớp nhau.

## Đầu ra

Trình bày cho người dùng theo thứ tự sau:

1. **Tóm tắt:** nguyên văn từ BRD.
2. **Danh sách màn hình:** tên nghiệp vụ, mới hay sửa, các hành vi chính.
3. **Bảng mapping API.**
4. **Mâu thuẫn & thiếu sót.**
5. **Câu hỏi,** nhóm theo người cần hỏi: `BA`, `Designer`, `BE`.
   - Mỗi câu hỏi nêu bối cảnh ngắn (tài liệu nào, chỗ nào). Người dùng sẽ chuyển câu hỏi đi, nên câu hỏi phải tự hiểu được mà không cần xem lại cuộc trò chuyện.
   - Nếu có thể, đưa luôn các phương án để người được hỏi chỉ cần chọn.

Sau đó hỏi người dùng có cho lưu kết quả vào `.claude/features/<mã>.md` không, theo mẫu [ghi-chu-tinh-nang-template.md](ghi-chu-tinh-nang-template.md).
- Lưu nguyên văn phần BRD và BE Specs người dùng đã paste, để các phiên sau không phải paste lại.
- Khi người dùng mang câu trả lời về, cập nhật vào bảng "Câu hỏi & trả lời", ghi kèm ngày.

## Lỗi thường gặp

| Sai | Đúng |
|---|---|
| BRD không nói thông báo lỗi là gì nên tự viết một câu | Đưa thành câu hỏi cho BA |
| Figma thiếu trạng thái rỗng nên tự thiết kế | Đưa thành câu hỏi cho Designer |
| BE thiếu field nhưng vẫn coi như có | Đưa thành câu hỏi cho BE |
| Câu hỏi kiểu "Chỗ này thế nào?" | Nêu rõ tài liệu, vị trí, vấn đề, và các phương án |
| Đoán thông số thiết kế từ ảnh | Xin số liệu chính xác qua Figma MCP hoặc tab Inspect |
