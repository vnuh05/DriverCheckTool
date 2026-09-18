# DriverCheckTool — công cụ đọc máy cắm cáp

Công cụ nhỏ chạy trên máy tính ở quầy. Khi nhân viên cắm điện thoại của khách vào máy
tính bằng cáp USB, trang web bán hàng đọc được **tên máy, model, số serial và IMEI**
để điền sẵn vào đơn — không phải gõ tay 15 chữ số, không đọc nhầm số.

Repo này chỉ dùng để **phát hành bản cài**. Tệp tải về nằm ở mục
[Releases](../../releases/latest).

---

## Dùng thế nào

Nhân viên **không cần tải tay** tệp ở đây.

1. Mở trang nhập đơn điện thoại trên web, ở dòng hàng bấm **Đọc từ máy đang cắm cáp**.
2. Máy tính chưa có công cụ thì web hiện nút **Tải công cụ**. Mở tệp vừa tải.
3. Windows hỏi *"Windows đã bảo vệ PC của bạn"* (tệp chưa ký số) → bấm
   **Thông tin thêm → Vẫn chạy**.
4. Cửa sổ đen tự cài rồi báo **Xong**. Không cần quyền quản trị, không cần khởi động lại.
5. Quay lại web, bấm đọc máy lần nữa.

Mỗi máy tính chỉ cài **một lần**. Từ đó công cụ tự chạy ẩn mỗi khi mở máy.

### Trên điện thoại

| Loại máy | Cần làm |
|---|---|
| **Android** | Cài đặt → Thông tin điện thoại → Thông tin phần mềm → bấm 7 lần vào *Số hiệu bản tạo*. Vào *Tùy chọn nhà phát triển* → bật **Gỡ lỗi USB**. Cắm cáp, bấm **Cho phép** trên máy. Xong việc nên tắt lại Gỡ lỗi USB. |
| **iPhone** | Cắm cáp, mở khoá máy, bấm **Tin cậy** và nhập mật mã (chỉ lần đầu). Máy tính cần có ứng dụng **Apple Devices** (Microsoft Store) hoặc iTunes. |

---

## Công cụ đọc được gì

| | Android | iPhone |
|---|---|---|
| Tên thương mại (Galaxy A16 5G, iPhone 13…) | ✅ | ✅ |
| Model / mã sản phẩm | ✅ | ✅ (part number, ví dụ `MQ8V3LL/A`) |
| Số serial | ✅ | ✅ |
| IMEI (cả IMEI thứ hai nếu có) | ✅ xem ghi chú dưới | ✅ |
| Phiên bản hệ điều hành | ✅ | ✅ |
| Màu, dung lượng pin | — | ✅ |
| Máy mới / tân trang / thay bảo hành | — | ✅ |

**IMEI trên Android đời mới.** Android 10 trở lên chặn đọc IMEI. Khi đó web hiện nút
**Thử lấy IMEI bằng \*#06#**: công cụ mở bàn phím gọi trên chính máy đó, gõ `*#06#` rồi đọc
số trên màn hình. Máy phải đang mở khoá. Cách này chạy trên Samsung One UI; không chạy
trên trình quay số của MIUI (Xiaomi) — nhưng Xiaomi thường đọc được IMEI bằng cách khác.

Mọi IMEI đều được kiểm tra số kiểm tra (Luhn) trước khi trả về. Số sai không bao giờ được
điền vào đơn.

---

## An toàn

Phần này trả lời câu hỏi "cài cái này vào máy quầy có sao không".

- **Chỉ nghe trong máy.** Công cụ mở một cổng ở `127.0.0.1:47321`. Địa chỉ `127.0.0.1`
  chỉ gọi được từ chính máy tính đó — máy khác trong mạng, hay ai trên Internet, đều
  không chạm tới được.
- **Chỉ trả lời trang web bán hàng.** Trang lạ mở trên cùng máy cũng không đọc được: công
  cụ chỉ trả lời những địa chỉ web có trong danh sách cho phép (trang bán hàng chính thức),
  và trình duyệt tự chặn mọi trang khác.
- **Chỉ đọc.** Công cụ không ghi, không xoá, không cài gì lên điện thoại. Nó chỉ hỏi máy
  thông tin nhận dạng. Riêng cách lấy IMEI bằng `*#06#` có mở bàn phím gọi trên máy, rồi
  tự đóng lại và xoá tệp tạm nó tạo ra.
- **Không gửi dữ liệu đi đâu.** Thông tin đọc được chỉ trả về cho trang web đang mở trên
  cùng máy. Công cụ không tự kết nối ra Internet.
- **Không cần quyền quản trị.** Cài vào thư mục của người dùng
  (`%LOCALAPPDATA%\KKM\device-agent`), không đụng tới hệ thống.
- **Không có khoá, mật khẩu hay dữ liệu khách** trong tệp cài.

Lúc cài, trình cài đặt tải thêm đúng hai thứ:

1. `kkm-agent.exe` — từ mục Releases của repo này.
2. `platform-tools` (chứa `adb`, dùng để nói chuyện với Android) — tải thẳng từ máy chủ của
   Google: `https://dl.google.com/android/repository/platform-tools-latest-windows.zip`.

### Kiểm tra tệp không bị tráo

Mỗi bản phát hành ghi mã SHA-256 của `kkm-agent.exe`. Kiểm bằng PowerShell:

```powershell
Get-FileHash "$env:LOCALAPPDATA\KKM\device-agent\kkm-agent.exe" -Algorithm SHA256
```

Kết quả phải trùng với mã ghi trong phần mô tả của bản phát hành. Không trùng thì **đừng
chạy**, báo IT.

### Vì sao Windows cảnh báo

Tệp chưa được ký số bằng chứng chỉ của nhà phát hành (chứng chỉ ký mã phải mua theo năm),
nên SmartScreen hỏi lại lần đầu. Kiểm mã SHA-256 như trên là cách chắc chắn tệp đúng là bản
phát hành ở đây.

---

## Xem, dừng, gỡ

| Việc | Cách làm |
|---|---|
| Xem công cụ có đang chạy không | Mở <http://127.0.0.1:47321/kkm/v1/trang-thai> trong trình duyệt. Thấy `"ok": true` là đang chạy. |
| Xem nhật ký | `%LOCALAPPDATA%\KKM\device-agent\agent.log` |
| Tắt tạm | Task Manager → `kkm-agent.exe` → End task. Lần mở máy sau sẽ tự chạy lại. |
| Không cho tự chạy nữa | Win + R → `shell:startup` → xoá *KKM - cong cu doc may*. |
| Gỡ hẳn | Tắt `kkm-agent.exe`, xoá shortcut như trên, rồi xoá thư mục `%LOCALAPPDATA%\KKM\device-agent`. |

---

## Gặp lỗi

| Hiện tượng | Cách xử lý |
|---|---|
| Web vẫn hiện "chưa có công cụ" sau khi cài | Mở đường dẫn kiểm tra ở bảng trên. Không mở được thì chạy lại tệp cài. |
| "Không thấy máy nào đang cắm" | Dùng cáp có truyền dữ liệu (cáp chỉ sạc không được). Mở khoá máy. Android: đã bật Gỡ lỗi USB và bấm Cho phép chưa. iPhone: đã bấm Tin cậy chưa. |
| Android báo "chưa bấm Cho phép" | Rút cáp, cắm lại, nhìn màn hình điện thoại và bấm **Cho phép** (nên tích *Luôn cho phép từ máy tính này*). |
| iPhone báo "chưa tin cậy" | Mở khoá iPhone, bấm **Tin cậy**, nhập mật mã. Máy tính phải có Apple Devices hoặc iTunes. |
| Không lấy được IMEI | Android đời mới: bấm **Thử lấy IMEI bằng \*#06#** trên web. Vẫn không được thì bấm `*#06#` trên máy và nhập tay. |

---

## Yêu cầu

- Windows 10 hoặc 11, 64-bit.
- Trình duyệt Chrome hoặc Edge.
- iPhone: ứng dụng **Apple Devices** (Microsoft Store) hoặc iTunes trên máy tính.
