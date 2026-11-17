---
name: write-report
description: Dùng khi người dùng cần viết báo cáo sau khi phát triển xong một tính năng, khi nói "viết báo cáo", "report tính năng", "tổng hợp những gì đã code", hoặc cần mô tả chi tiết thay đổi theo từng màn hình và các API đã gọi.
---

# Viết báo cáo tính năng

## Mục tiêu

Viết báo cáo mô tả chi tiết những gì đã code cho một tính năng, theo đúng format người dùng hay dùng.

**Nguyên tắc cốt lõi:** báo cáo chỉ chứa những gì thực sự có trong diff của nhánh tính năng. Không thêm màn hình hay API nào không có trong code. Không đoán tác dụng của API khi chưa có căn cứ.

## Đầu vào

1. **Hồ sơ dự án** `CLAUDE.local.md`: lấy nhánh gốc, nơi khai báo route, tầng gọi API. Nếu repo chưa có file này, dùng skill `onboard-project` trước.
2. **Ghi chú tính năng** `.claude/features/<mã>.md` (nếu có): đã chứa sẵn tóm tắt BRD, BE Specs, danh sách nhánh và commit.
3. **Tóm tắt từ BRD:**
   - Nếu ghi chú tính năng hoặc cuộc trò chuyện đã có BRD, lấy nguyên văn phần tóm tắt của BRD.
   - Nếu chưa có, xin người dùng paste vào.
   - Không tự viết tóm tắt.
4. **BE Specs** (nếu có trong ghi chú tính năng hoặc cuộc trò chuyện): dùng để mô tả tác dụng của từng endpoint.
5. **Phạm vi diff:** nhánh tính năng so với nhánh gốc mà nó được checkout ra (xem trong hồ sơ dự án).
   - Nếu không chắc nhánh gốc là nhánh nào, hỏi người dùng.
   - Nếu có thay đổi chưa commit, hỏi người dùng có tính vào báo cáo không.

## Quy trình

1. Hỏi người dùng hai thông tin: nhánh nào chứa đầy đủ code của tính năng, và lấy diff so với mốc nào (nhánh hoặc commit gốc). Sau đó chạy `git diff <mốc> <nhánh tính năng> --stat` để có danh sách file thay đổi, rồi đọc diff chi tiết.
   - Nếu nhánh tính năng đã được merge vào nhánh gốc thì `<nhánh gốc>...HEAD` sẽ ra rỗng. Gặp trường hợp này thì hỏi người dùng commit gốc, hoặc tìm bằng `git reflog show <nhánh tính năng>`.
2. **Xác định màn hình.** Với mỗi file UI có thay đổi, lần theo chuỗi import ngược lên tới chỗ khai báo route để biết file thuộc màn hình nào.
   - Đường dẫn ghi theo đúng cách khai báo trong router, không có domain. Giữ nguyên tham số động, ví dụ `/orders/:id`.
   - Một component dùng chung nằm trong nhiều màn hình thì liệt kê ở mọi màn hình bị ảnh hưởng.
3. **Đặt tên màn hình** bằng tiếng Việt theo nghiệp vụ, dựa vào BRD hoặc nội dung màn hình (ví dụ: "Danh sách đơn hàng", "Chi tiết khách hàng"). Không dùng tên component.
4. **Xác định API.** Lấy các lời gọi API được thêm mới hoặc sửa trong diff, lần tới chỗ định nghĩa để có method và URL.
   - URL ghi phần path, không ghi domain. Path lấy theo BE Specs nếu có, không thì lấy theo code. Thay biến trong path bằng tham số, ví dụ `/api/v1/orders/{id}`.
   - Tác dụng của API lấy từ BE Specs. Nếu không có BE Specs, suy ra từ code và đánh dấu `(cần xác nhận)`.
5. **Trình bày** theo format bên dưới, rồi liệt kê riêng các chỗ đang đánh dấu `(cần xác nhận)` để người dùng kiểm tra.

## Format

Giữ đúng format sau: tiếng Việt, thụt lề 3 dấu cách cho cấp con, mỗi màn hình một mục.

```
<Tóm tắt lấy từ BRD>

Chi tiết thay đổi:

* Màn hình <Tên màn hình tiếng Việt> (<đường dẫn không gồm domain>):
   * Call API <METHOD> <url> để <tác dụng của endpoint>
   * Call API <METHOD> <url> để <tác dụng của endpoint>
* Màn hình <Tên màn hình khác> (<đường dẫn>):
   * Call API <METHOD> <url> để <tác dụng của endpoint>
```

Ví dụ:

```
Bổ sung chức năng huỷ đơn hàng cho khách hàng khi đơn chưa được giao.

Chi tiết thay đổi:

* Màn hình Chi tiết đơn hàng (/orders/:id):
   * Call API GET /api/v1/orders/{id} để lấy thông tin chi tiết đơn hàng
   * Call API POST /api/v1/orders/{id}/cancel để huỷ đơn hàng
```

**Màn hình chỉ sửa giao diện, không gọi API mới hay API bị sửa:** ghi một dòng duy nhất theo mẫu dưới, không có dòng con.

```
* Giao diện màn hình (<đường dẫn không gồm domain>)
```

Màn hình vừa sửa giao diện vừa có API mới hoặc API bị sửa thì dùng format chuẩn "Màn hình … " ở trên, với các dòng con "Call API".

Ví dụ đầy đủ:

```
Bổ sung chức năng huỷ đơn hàng cho khách hàng khi đơn chưa được giao.

Chi tiết thay đổi:

* Màn hình Chi tiết đơn hàng (/orders/:id):
   * Call API POST /api/v1/orders/{id}/cancel để huỷ đơn hàng
* Giao diện màn hình (/orders)
```

## Lỗi thường gặp

| Sai | Đúng |
|---|---|
| Tự viết lại phần tóm tắt | Lấy nguyên phần tóm tắt từ BRD |
| Ghi tên component (`OrderDetailPage`) | Ghi tên nghiệp vụ tiếng Việt ("Chi tiết đơn hàng") |
| Ghi đường dẫn đầy đủ `https://.../orders/1` | Ghi `/orders/:id` |
| Liệt kê cả API cũ đã có từ trước, không bị thay đổi | Chỉ liệt kê API được thêm mới hoặc sửa trong diff |
| Đoán tác dụng của API rồi viết như thật | Dùng BE Specs, không có thì đánh dấu `(cần xác nhận)` |
