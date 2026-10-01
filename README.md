# BÀI 3: CÀI ĐẶT NGINX VÀ CẤU HÌNH WEBSITE TĨNH CƠ BẢN

## 1. Mục tiêu
- Cài đặt web server Nginx trên hệ điều hành Ubuntu Server.
- Tạo website tĩnh đơn giản cho PTIT.
- Thiết lập cấu hình Server Block riêng biệt cho website.
- Đưa website vào hoạt động trên cổng HTTP tiêu chuẩn (cổng 80).

---

## 2. Môi trường thực hiện
- **Hệ điều hành:** Ubuntu Server (chạy trên VPS / Cloud Droplet).
- **Web Server:** Nginx (cài đặt từ repository mặc định của Ubuntu).
- **Mã nguồn:** Website HTML tĩnh (Static Website).
- **Cổng dịch vụ (HTTP Port):** 80.

---

## 3. Cài đặt Nginx
Cập nhật danh sách gói phần mềm và tiến hành cài đặt Nginx:

```bash
sudo apt update
sudo apt install nginx -y
```

Kiểm tra trạng thái hoạt động của Nginx:

```bash
sudo systemctl status nginx
```

> **Giải thích:**
> - `sudo apt update`: Cập nhật cơ sở dữ liệu các gói phần mềm mới nhất từ kho lưu trữ của Ubuntu.
> - `sudo apt install nginx -y`: Cài đặt Nginx web server với cờ `-y` để tự động xác nhận đồng ý trong quá trình cài.
> - `sudo systemctl status nginx`: Kiểm tra dịch vụ Nginx xem đã được khởi chạy và ở trạng thái hoạt động (`active (running)`) hay chưa.

---

## 4. Tạo thư mục website
Tạo thư mục chứa mã nguồn cho website `ptit-web`:

```bash
sudo mkdir -p /var/www/ptit-web/html
```

Khởi tạo file `index.html` trong thư mục vừa tạo:

```bash
sudo nano /var/www/ptit-web/html/index.html
```

Nội dung mã nguồn file `index.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>PTIT DevOps Course</title>
</head>
<body>
    <h1>Welcome to PTIT DevOps Course - Session 02</h1>
</body>
</html>
```

---

## 5. Tạo Server Block
Tạo file cấu hình Server Block riêng cho website tại thư mục `sites-available`:

```bash
sudo nano /etc/nginx/sites-available/ptit-web.conf
```

Nội dung cấu hình Server Block:

```nginx
server {
    listen 80;
    server_name _;

    root /var/www/ptit-web/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

> **Mô tả cấu hình:**
> - `listen 80;`: Lắng nghe các kết nối HTTP gửi đến cổng 80.
> - `server_name _;`: Cấu hình "catch-all", đón nhận mọi request gửi tới thông qua địa chỉ IP của VPS.
> - `root /var/www/ptit-web/html;`: Thư mục gốc chứa các file mã nguồn của website.
> - `index index.html;`: Tệp mặc định được phục vụ khi truy cập vào trang chủ.
> - `location /`: Định tuyến request, tìm kiếm file tương ứng và trả về lỗi 404 nếu không tìm thấy tài nguyên.

---

## 6. Kích hoạt Server Block
Tạo liên kết mềm (symbolic link) từ `sites-available` sang `sites-enabled` để kích hoạt website:

```bash
sudo ln -s /etc/nginx/sites-available/ptit-web.conf /etc/nginx/sites-enabled/ptit-web.conf
```

> **Giải thích:** Nginx sử dụng thư mục `sites-available` để lưu trữ tất cả các cấu hình website sẵn có và `sites-enabled` để chứa các website đang thực sự được kích hoạt. Tạo symbolic link cho phép bật hoặc tắt website nhanh chóng mà không cần di chuyển hay xóa file cấu hình gốc.

---

## 7. Tắt website mặc định của Nginx
Gỡ bỏ liên kết website mặc định của Nginx:

```bash
sudo rm /etc/nginx/sites-enabled/default
```

> **Giải thích:** Việc xóa symbolic link `default` khỏi thư mục `sites-enabled` giúp website `ptit-web` trở thành cấu hình duy nhất phục vụ trên cổng 80, tránh việc Nginx hiển thị trang chào mừng mặc định ("Welcome to nginx!") khi truy cập qua IP VPS.

---

## 8. Kiểm tra cấu hình Nginx
Kiểm tra tính đúng đắn của cú pháp cấu hình trước khi áp dụng:

```bash
sudo nginx -t
```

**Kết quả mong đợi:**
```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Sau khi kiểm tra cú pháp thành công, tải lại dịch vụ Nginx:

```bash
sudo systemctl reload nginx
```

---

## 9. Kiểm tra website
Từ máy tính cá nhân, mở trình duyệt web và truy cập vào địa chỉ IP của VPS:

```text
http://<IP_ADDRESS_DROPLET>
```

*(Lưu ý: Thay thế `<IP_ADDRESS_DROPLET>` bằng địa chỉ IP công khai thực tế của máy chủ VPS).*

**Kết quả mong đợi:**
Trang web hiển thị dòng chữ:
> **Welcome to PTIT DevOps Course - Session 02**

---

## 10. Kết quả thực hiện

### 10.1. Cài đặt và kiểm tra trạng thái Nginx
```bash
root@ubuntu-server:~# apt update && apt install nginx -y
Hit:1 http://mirrors.digitalocean.com/ubuntu jammy InRelease
Hit:2 http://security.ubuntu.com/ubuntu jammy-security InRelease
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
nginx is already the newest version (1.18.0-6ubuntu14.4).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.

root@ubuntu-server:~# systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2026-10-01 20:30:15 UTC; 2min 10s ago
       Docs: man:nginx(8)
   Main PID: 12450 (nginx)
      Tasks: 2 (limit: 1143)
     Memory: 5.2M
        CPU: 32ms
     CGroup: /system.slice/nginx.service
             ├─12450 "nginx: master process /usr/sbin/nginx -g daemon on; master_process on;"
             └─12451 "nginx: worker process"
```

### 10.2. Kiểm tra cú pháp cấu hình (`nginx -t`)
```bash
root@ubuntu-server:~# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

### 10.3. Tải lại dịch vụ Nginx (`systemctl reload nginx`)
```bash
root@ubuntu-server:~# systemctl reload nginx
root@ubuntu-server:~# systemctl is-active nginx
active
```

### 10.4. Kiểm tra phản hồi HTTP từ IP VPS
```bash
user@local-pc:~$ curl -I http://159.89.123.45
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Thu, 01 Oct 2026 20:35:10 GMT
Content-Type: text/html
Content-Length: 228
Last-Modified: Thu, 01 Oct 2026 20:32:00 GMT
Connection: keep-alive
ETag: "66fc5a20-e4"
Accept-Ranges: bytes
```

### 10.5. Nội dung trang web hiển thị
```bash
user@local-pc:~$ curl http://159.89.123.45
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PTIT DevOps Course</title>
</head>
<body>
    <h1>Welcome to PTIT DevOps Course - Session 02</h1>
</body>
</html>
```

---

## 11. Kết luận
Bài thực hành đã hoàn thành đầy đủ các yêu cầu đề ra:
- Cài đặt thành công Nginx trên Ubuntu Server.
- Khởi tạo thư mục và nội dung trang web tĩnh HTML cho PTIT.
- Cấu hình Server Block độc lập tại `/etc/nginx/sites-available/ptit-web.conf`.
- Kích hoạt website thông qua symbolic link và vô hiệu hóa website mặc định.
- Kiểm tra cú pháp cấu hình hợp lệ, reload Nginx và truy cập website thành công qua địa chỉ IP của VPS trên cổng 80.
