# (Bài tập tổng hợp) Ngày Đầu Thực Tập Dev Tại Công Ty Công Nghệ
## Thiết lập Proflie và Bookmark chuẩn Dev.
**1. Dùng chung 1 Profile cho 2 GitHub**
Cả hai tài khoản dùng chung một kho cookie của `github.com`. Cookie đăng nhập (`user_session`) của tài khoản sau **ghi đè** tài khoản trước, nên không đăng nhập song song được: đăng nhập tài khoản này thì tài khoản kia bị văng (Session Conflict). Ngoài ra dễ nhầm tài khoản khi push code, và dữ liệu công ty lẫn với dữ liệu cá nhân.

**2. Mô hình 3 Profile**

| Profile | Tên đặt | Mục đích |
|---|---|---|
| 1 | Personal | Mạng xã hội, GitHub cá nhân, việc riêng |
| 2 | Company_Work | GitHub công ty, `test.internal-tool.com`, tài liệu công việc |
| 3 | Sandbox/Test | Thử extension mới, link lạ, thử nghiệm không ảnh hưởng 2 Profile kia |

**3. Bookmark (3 thư mục, mỗi thư mục 2 trang)**
- 📁 **01_Work**: GitHub công ty, test.internal-tool.com
- 📁 **02_Docs**: MDN Web Docs, Stack Overflow
- 📁 **03_Tools**: Regex101, Can I Use

**4. Đồng nghiệp mượn máy 5 phút**
Dùng **Guest Profile (Chế độ khách)**. Đây là phiên tách hẳn, không thấy bookmark, mật khẩu, lịch sử và đăng nhập của mình, đóng cửa sổ là xóa sạch. Incognito thì vẫn chạy từ Profile của mình nên kém an toàn hơn.

## Phần B: Extension

**1.** Cài nhóm **Dev Suite**, tiện ích **JSON Viewer** (hoặc JSONVue), vì nó định dạng JSON thô thành dạng cây dễ đọc.
- *Productivity Suite* (quản lý tab, ghi chú, ...) không xử lý được JSON.
- *Security Suite* (chặn quảng cáo, quản lý mật khẩu, ...) cũng không liên quan.

**2.** Chọn Site Access là **On click**. Theo nguyên tắc quyền tối thiểu, extension chỉ được chạy khi mình bấm, không đọc dữ liệu của mọi trang (kể cả trang nội bộ công ty). **On all sites** có rủi ro cao hơn.

## Phần C: Phân tích luồng HTTP Request 

**1. 4 giai đoạn:**
1. **DNS lookup**: đổi tên miền thành địa chỉ IP.
2. **Kết nối TCP (và TLS/HTTPS)**: thiết lập kết nối đến server.
3. **HTTP Request / Response**: trình duyệt gửi yêu cầu, server trả về HTML, CSS, JS.
4. **Browser Rendering**: trình duyệt dựng và hiển thị trang.

**2. So sánh mã trạng thái**

| Mã | Ý nghĩa | Lỗi nằm ở đâu? | Hướng debug ban đầu |
|---|---|---|---|
| 404 Not Found | Server không tìm thấy tài nguyên | Phía client (URL/đường dẫn sai) | Kiểm tra lại URL, đường dẫn file/API, lỗi chính tả |
| 500 Internal Server Error | Server gặp lỗi khi xử lý | Phía server (code backend) | Xem Response và báo backend kiểm tra log server |

**3.** Mở tab **Network** để xem mã trạng thái (cột Status). Elements chỉ xem cấu trúc HTML/CSS, Console xem log và lỗi JS, Application xem cookie và storage, nên không có thông tin về HTTP request.

## Phần D: Chuẩn đoán Cache và Cookies

**1.** Nguyên nhân: trình duyệt đang dùng **file CSS cũ trong Cache**. Cách khắc phục: **Hard Reload (`Ctrl + Shift + R`)**, hoặc mở DevTools, chuột phải nút Reload chọn **Empty Cache and Hard Reload**.

**2.** Cookie `session_id` không có HttpOnly nên JavaScript đọc được bằng `document.cookie`. Rủi ro là tấn công **XSS (Cross-Site Scripting)**: kẻ xấu chèn script lấy cookie, rồi chiếm phiên đăng nhập (Session Hijacking).

**3.** Mở **F12 → Application → Cookies**, chọn domain rồi xóa cookie `session_id` (hoặc chỉ tick **Cookies** khi Clear browsing data). Cách này chỉ xóa phiên đăng nhập, Cache ảnh/CSS vẫn giữ nguyên.

## Phần E: Debug 
**1. Sửa màu nút "Đăng nhập" trên trình duyệt:**
Chuột phải nút → **Inspect** (hoặc F12) → tab **Elements** → chọn phần tử nút → ở khung **Styles**, bấm vào ô màu của `color`/`background-color` và đổi thành mã màu theo thiết kế. Thay đổi chỉ có trên trình duyệt, F5 là mất, file gốc không bị sửa. Ghi lại mã màu đúng để báo Dev.

**2. Request đỏ trên Network, 4 bước debug:**
1. **Xác định**: tìm dòng đỏ, ghi lại URL, method và mã trạng thái.
2. **Kiểm tra Headers**: xem Request URL, Status Code, Request Headers.
3. **Xem Response/Payload**: đọc nội dung lỗi server trả về và dữ liệu đã gửi.
4. **Kết luận và báo cáo**: 4xx là lỗi phía client (kiểm tra URL, dữ liệu gửi), 5xx là lỗi phía server (báo backend kèm ảnh chụp, URL, mã lỗi).

*(Nếu bài giảng đặt tên 4 bước khác, bạn chỉnh lại cho khớp.)*

**3. Hai thao tác trên tab Network:**
- Chọn bộ lọc **Fetch/XHR** để chỉ xem các lời gọi API.
- Tick **Disable cache** để luôn lấy dữ liệu mới, không lấy từ bộ nhớ đệm.
