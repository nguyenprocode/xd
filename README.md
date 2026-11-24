<div align="center">

<img src="public/img/banner.png" alt="My NAS" width="100%" />

# My NAS

**Biến repo GitHub của bạn thành một NAS cá nhân, gọn nhẹ và không cần cài thư viện nào.**

Tải lên là commit vào repo. Tải về là lấy lại từ GitHub.

[![Node.js](https://img.shields.io/badge/Node.js-18%2B-339933?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Zero Dependencies](https://img.shields.io/badge/dependencies-0-brightgreen?style=for-the-badge)](#tính-năng)
[![License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge)](#giấy-phép)

[Tính năng](#tính-năng) ·
[Cài đặt](#cài-đặt-nhanh) ·
[Cấu hình](#cấu-hình) ·
[Đăng nhập](#đăng-nhập) ·
[Triển khai](#đưa-lên-mạng) ·
[Lưu ý](#lưu-ý-quan-trọng)

</div>

---

## Tính năng

| Tính năng | Mô tả |
|---|---|
| **Trang chủ** | Dung lượng đã dùng, thư mục, tệp gần đây và trạng thái máy chủ (CPU, RAM, thời gian chạy) |
| **Tệp và thư mục** | Duyệt thư mục, tạo thư mục mới, tải tệp về |
| **Kéo thả tải lên** | Thả file vào trang là tải lên, có thanh tiến trình |
| **Tìm kiếm nhanh** | Nhấn `Ctrl` + `K` (hoặc `Cmd` + `K`) ở bất cứ đâu |
| **Thùng rác** | Xóa nhầm vẫn khôi phục lại được, hoặc xóa vĩnh viễn |
| **Liên kết chia sẻ** | Chia sẻ tệp qua link có hạn dùng từ 1 đến 90 ngày |
| **Thông báo** | Chuông thông báo nhận tin từ tác giả |
| **Giao diện tối / sáng** | Mặc định là tối, đổi trong phần Cài đặt |
| **Dùng trên điện thoại** | Giao diện responsive |

> [!TIP]
> Không cần `npm install`. Dự án không có thư viện phụ thuộc, chỉ cần **Node.js 18+** là chạy được ngay.

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

Mở `.env` và điền ít nhất 3 giá trị:

```env
GITHUB_TOKEN=github_pat_xxxxxxxxxxxxxxxx
GITHUB_REPO=tenban/kho-file
APP_PASSWORD=doi-mat-khau-nay
```

### 4. Chạy

```bash
node server.js
```

Hoặc:

```bash
npm start
```

### 5. Truy cập

Mở **http://localhost:3000** và đăng nhập:

- **Tên đăng nhập:** tên GitHub của token (máy chủ tự hỏi GitHub, không cần khai báo)
- **Mật khẩu:** giá trị `APP_PASSWORD`

> [!NOTE]
> Mặc định file được lưu trong thư mục `files/` của nhánh `main`. Bạn có thể đổi bằng `GITHUB_FOLDER` và `GITHUB_BRANCH`.

---

## Cấu hình

Các biến đặt trong file `.env` (hoặc biến môi trường của dịch vụ bạn triển khai).

| Biến | Bắt buộc | Mặc định | Ý nghĩa |
|---|---|---|---|
| `GITHUB_TOKEN` | Có | | Token GitHub có quyền Contents: Read and write |
| `APP_PASSWORD` | Có | | Mật khẩu đăng nhập web |
| `GITHUB_REPO` | Có | | Repo lưu file, dạng `tenban/ten-repo` |
| `GITHUB_BRANCH` | Không | `main` | Nhánh lưu file |
| `GITHUB_FOLDER` | Không | `files` | Thư mục lưu file trong repo |
| `PORT` | Không | `3000` | Cổng máy chủ |
| `MAX_UPLOAD_MB` | Không | `50` | Dung lượng tối đa mỗi tệp tải lên (MB) |
| `STORAGE_LIMIT_GB` | Không | `5` | Hạn mức dung lượng của kho (GB) |
| `AUTHOR_NAME` | Không | tên GitHub | Tên hiển thị của chủ kho |

---

## Đăng nhập

Web chỉ dùng **một tài khoản với một token duy nhất** (`GITHUB_TOKEN` + `APP_PASSWORD`). Các biến có hậu tố `_2`, `_3`... không còn được đọc.

| Hạng mục | Chi tiết |
|---|---|
| Tên đăng nhập | Tên GitHub của token |
| Mật khẩu | `APP_PASSWORD` |
| Chống dò mật khẩu | Sai quá **8 lần trong 10 phút** từ một địa chỉ IP sẽ bị tạm khóa |
| Thời hạn phiên | **1 ngày** (24 giờ), sau đó cần đăng nhập lại |

> [!WARNING]
> Đổi token hoặc mật khẩu thì **phiên đăng nhập và liên kết chia sẻ cũ sẽ mất hiệu lực**.

> [!NOTE]
> Địa chỉ IP được lấy từ header `X-Forwarded-For` trước tiên. Nếu chạy sau proxy (Nginx, Cloudflare...), hãy chắc chắn proxy ghi đè header này, nếu không giới hạn đăng nhập có thể bị né.

---

## Cấu trúc thư mục

```text
my-nas
├── server.js              Máy chủ Node (API + phục vụ file trong public/)
├── package.json           Thông tin dự án, lệnh npm start
├── .env.example           Mẫu cấu hình, sao chép thành .env
├── .gitignore             Bỏ qua .env và node_modules
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
        ├── logo.svg       Logo (thanh bên, trang đăng nhập và favicon)
        ├── banner.png     Ảnh nền banner ở trang chủ
        └── cloudup.png    Biểu tượng khung tải lên ở trang chủ
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

Chuông thông báo trên web nhận tin từ một file JSON công khai.

**Cách hoạt động**

1. Link **raw** tới file JSON được ghi cố định trong `server.js` (hằng số `NOTICE_URL`, có ghi chú không được xóa).
2. Nếu giá trị này còn chứa chữ `TEN_GITHUB` thì tính năng **tự tắt**.
3. Web đọc file khi mở trang, mỗi **10 phút** và khi mở chuông.
4. Thông báo mới được lưu vào trình duyệt, nên dù sau này bị xóa khỏi file raw thì người dùng vẫn còn thấy.

**Quy tắc**

- Mỗi thông báo cần một `id` **duy nhất**.
- Id đã nhận sẽ không hiện lại, kể cả khi người dùng bấm "Xóa hết".
- GitHub cache file raw khoảng 5 phút nên thông báo mới có thể hiện chậm một chút.

---

## Lưu ý quan trọng

**Thùng rác và dung lượng**

- Thùng rác nằm ở `files/.trash/` trong repo.
- "Xóa vĩnh viễn" chỉ gỡ file khỏi nhánh, bản cũ vẫn nằm trong lịch sử Git nên **dung lượng repo không giảm**.
- Mỗi lần tải lên, di chuyển hoặc xóa là một commit, nên lịch sử Git sẽ phình dần theo thời gian.

**Chia sẻ**

- Chỉ chia sẻ được **tệp**, không chia sẻ được thư mục.
- Hạn dùng của liên kết từ 1 đến 90 ngày, mặc định 7 ngày.
- Liên kết được ký bằng khóa suy ra từ token và mật khẩu. Ai có liên kết đều tải được file đến khi hết hạn.
- Muốn thu hồi sớm: đổi `GITHUB_TOKEN` hoặc `APP_PASSWORD` rồi khởi động lại.
- Liên kết chỉ mở được từ ngoài khi máy chủ truy cập được từ Internet.

**Trạng thái máy chủ**

- CPU, RAM và thời gian chạy trên trang chủ là của máy đang chạy `server.js`.
- Một số môi trường (ví dụ Termux) không cung cấp số CPU nên sẽ hiện dấu `—`.

**Giới hạn**

- Tải lên mặc định tối đa **50 MB** mỗi file (`MAX_UPLOAD_MB`).
- API Contents của GitHub không ổn định với file lớn, nên đừng đặt giá trị này quá cao.

> [!CAUTION]
> **Không bao giờ commit hoặc gửi file `.env` cho người khác.** File này chứa token và mật khẩu của bạn. Nếu lỡ lộ, hãy thu hồi token trên GitHub và tạo token mới.

**Phù hợp với**

| Nên dùng | Không nên dùng |
|---|---|
| Tài liệu | Video |
| Ảnh nhỏ | Bản sao lưu hàng GB |
| Bản sao lưu nhẹ | File dung lượng rất lớn |

---

## Giấy phép

[MIT](https://opensource.org/licenses/MIT)

<div align="center">

Nếu thấy hữu ích, hãy để lại một ngôi sao cho dự án nhé.

</div>
