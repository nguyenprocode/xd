<div align="center">

<img src="public/img/logo.svg" alt="My NAS logo" width="88" height="88" />

# My NAS

**Biến repo GitHub của bạn thành một NAS cá nhân, gọn nhẹ và không cần cài thư viện nào.**

Tải lên là commit vào repo. Tải về là lấy lại từ GitHub.

[![Node.js](https://img.shields.io/badge/Node.js-18%2B-339933?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Zero Dependencies](https://img.shields.io/badge/dependencies-0-brightgreen?style=for-the-badge)](#tính-năng)
[![GitHub](https://img.shields.io/badge/storage-GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com)

[Tính năng](#tính-năng) ·
[Cài đặt](#cài-đặt-nhanh) ·
[Đăng nhập](#đăng-nhập) ·
[Triển khai](#đưa-lên-mạng) ·
[Lưu ý](#lưu-ý-quan-trọng)

</div>

---

## Tính năng

| Tính năng | Mô tả |
|---|---|
| **Trang chủ** | Dung lượng, thư mục, tệp gần đây và trạng thái máy chủ |
| **Tệp và thư mục** | Duyệt và quản lý như một NAS thực thụ |
| **Kéo thả tải lên** | Thả file vào trang là xong |
| **Tìm kiếm nhanh** | Nhấn `Ctrl` + `K` ở bất cứ đâu |
| **Thùng rác** | Xóa nhầm vẫn khôi phục lại được |
| **Liên kết chia sẻ** | Có hạn dùng, tự hết hiệu lực |
| **Giao diện tối / sáng** | Đổi chủ đề chỉ với một cú nhấp |
| **Dùng trên điện thoại** | Giao diện responsive |

> [!TIP]
> Không cần `npm install`. Chỉ cần **Node.js 18+** là chạy được ngay.

---

## Cài đặt nhanh

### 1. Tạo repo lưu trữ

Tạo một repo GitHub để chứa file (nên chọn **Private**) và tạo sẵn nhánh `main` (chỉ cần có một commit, ví dụ file README).

### 2. Tạo token

Vào **GitHub > Settings > Developer settings > Fine-grained tokens**, chọn đúng repo vừa tạo và cấp quyền:

| Quyền | Mức |
|---|---|
| Contents | **Read and write** |

### 3. Cấu hình

Trong thư mục dự án:

```bash
cp .env.example .env
```

Mở `.env` và điền 3 giá trị:

```env
GITHUB_TOKEN=github_pat_xxxxxxxxxxxx
GITHUB_REPO=ten-ban/ten-repo
APP_PASSWORD=mat-khau-cua-ban
```

### 4. Chạy

```bash
node server.js
```

### 5. Truy cập

Mở **http://localhost:3000** và đăng nhập:

- **Tên đăng nhập:** tên GitHub của token (máy chủ tự hỏi GitHub, không cần khai báo)
- **Mật khẩu:** giá trị `APP_PASSWORD`

> [!NOTE]
> File được lưu trong thư mục `files/` của repo. Bạn có thể đổi bằng biến `GITHUB_FOLDER`.

---

## Đăng nhập

Web chỉ dùng **một tài khoản với một token duy nhất** (`GITHUB_TOKEN` + `APP_PASSWORD`). Các biến có hậu tố `_2`, `_3`... không còn được đọc.

| Hạng mục | Chi tiết |
|---|---|
| Tên đăng nhập | Tên GitHub của token |
| Mật khẩu | `APP_PASSWORD` |
| Chống dò mật khẩu | Sai quá **8 lần trong 10 phút** từ một IP sẽ bị tạm khóa |
| Thời hạn phiên | **30 ngày** |

> [!WARNING]
> Đổi token hoặc mật khẩu thì **phiên đăng nhập và liên kết chia sẻ cũ sẽ mất hiệu lực**.

---

## Cấu trúc thư mục

```text
my-nas
├── server.js              Máy chủ Node (API + phục vụ file trong public/)
└── public
    ├── index.html         Trang chính (sau khi đăng nhập)
    ├── login.html         Trang đăng nhập
    ├── css
    │   ├── base.css       Biến màu, nút, ô nhập dùng chung
    │   ├── app.css        Giao diện trang chính
    │   └── login.css      Giao diện trang đăng nhập
    ├── js
    │   ├── theme.js       Áp dụng giao diện sáng/tối (cả hai trang)
    │   ├── guard.js       Chưa đăng nhập thì chuyển sang login.html
    │   ├── login.js       Xử lý đăng nhập
    │   ├── icons.js       Bộ biểu tượng và biểu tượng tệp
    │   └── app.js         Logic trang chính
    └── img
        └── logo.svg       Logo (thanh bên, trang đăng nhập và favicon)
```

Mở http://localhost:3000 sẽ tự chuyển sang `/login.html` nếu bạn chưa đăng nhập.

---

## Đưa lên mạng

Chạy trên bất kỳ máy chủ Node nào: **VPS**, **Render**, **Railway**, **Fly.io**...

Đặt các biến trong `.env` thành biến môi trường của dịch vụ đó.

> [!IMPORTANT]
> Hãy **bật HTTPS**, vì mật khẩu được gửi trong header của mỗi request.

---

## Thông báo từ tác giả

Tính năng này cho phép tác giả gửi thông báo đến người dùng qua một file JSON công khai.

**Cách hoạt động**

1. Link **raw** tới file JSON được ghi cố định trong `server.js` (hằng số `NOTICE_URL`, có ghi chú không được xóa).
2. Tác giả thay `TEN_GITHUB/TEN_REPO` bằng repo của mình và đặt file `notices.json` ở đó.
3. Nếu còn để nguyên chữ `TEN_GITHUB` thì tính năng **tự tắt**.
4. Web đọc file khi mở trang, mỗi **10 phút** và khi mở chuông.
5. Thông báo mới được lưu vào trình duyệt, nên dù sau này bị xóa khỏi file raw thì người dùng vẫn còn thấy.

**Quy tắc**

- Mỗi thông báo cần một `id` **duy nhất**.
- Id đã nhận sẽ không hiện lại, kể cả khi người dùng bấm "Xóa hết".
- GitHub cache file raw khoảng 5 phút nên thông báo mới có thể hiện chậm một chút.

---

## Lưu ý quan trọng

**Thùng rác và dung lượng**

- Thùng rác nằm ở `files/.trash/` trong repo.
- "Xóa vĩnh viễn" chỉ gỡ file khỏi nhánh, bản cũ vẫn nằm trong lịch sử Git nên **dung lượng repo không giảm**.
- Mỗi lần tải lên hoặc xóa là một commit, nên lịch sử Git sẽ phình dần theo thời gian.

**Chia sẻ**

- Liên kết chia sẻ được ký bằng khóa suy ra từ token và mật khẩu của tài khoản.
- Ai có liên kết đều tải được file đến khi hết hạn.
- Muốn thu hồi sớm: đổi một trong hai giá trị trên và khởi động lại.
- Liên kết chỉ mở được từ ngoài khi máy chủ truy cập được từ Internet.

**Trạng thái máy chủ**

- CPU, RAM và thời gian chạy trên trang chủ là của máy đang chạy `server.js`.
- Một số môi trường (ví dụ Termux) không cung cấp số CPU nên sẽ hiện dấu `—`.

**Giới hạn**

- Tải lên mặc định tối đa **50 MB** mỗi file (`MAX_UPLOAD_MB`).
- API Contents của GitHub không ổn định với file lớn, nên đừng đặt giá trị này quá cao.

> [!CAUTION]
> **Không bao giờ commit file `.env` lên GitHub.** File này chứa token và mật khẩu của bạn.

**Phù hợp với**

| Nên dùng | Không nên dùng |
|---|---|
| Tài liệu | Video |
| Ảnh nhỏ | Bản sao lưu hàng GB |
| Bản sao lưu nhẹ | File dung lượng rất lớn |

---

<div align="center">

Nếu thấy hữu ích, hãy để lại một ngôi sao cho dự án nhé.

</div>
