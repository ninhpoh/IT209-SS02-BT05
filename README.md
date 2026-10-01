# Bài 5: Quản lý quyền sở hữu thư mục Web

## 1. Mục tiêu

Cấu hình `/var/www/ptit-web/` để user `devops` có thể chỉnh sửa website không cần `sudo`, đồng thời Nginx (`www-data`) có thể đọc và phục vụ website.

## 2. Tạo user devops

```bash
id devops
```

Kết quả:

```text
uid=1000(devops) gid=1000(devops) groups=1000(devops)
```

## 3. Tạo thư mục Web

```bash
mkdir -p /var/www/ptit-web/html
echo "<h1>PTIT Web - Bài 5</h1>" > /var/www/ptit-web/html/index.html
```

## 4. Đổi quyền sở hữu

```bash
chown -R devops:www-data /var/www/ptit-web
```

## 5. Phân quyền

```bash
find /var/www/ptit-web -type d -exec chmod 750 {} \;
find /var/www/ptit-web -type f -exec chmod 640 {} \;
```

- `devops`: đọc + ghi.
- `www-data`: đọc.
- User khác: không có quyền.

## 6. Kiểm tra quyền thư mục

```bash
ls -la /var/www/ptit-web/
```

Kết quả thực tế:

```text
drwxr-x--- 3 devops www-data ... .
drwxr-xr-x 3 root root ... ..
drwxr-x--- 3 devops www-data ... html
```

Kiểm tra file:

```bash
ls -la /var/www/ptit-web/html/
```

Kết quả:

```text
drwxr-x--- 2 devops www-data ... .
drwxr-x--- 3 devops www-data ... ..
-rw-r----- 1 devops www-data ... index.html
```

## 7. Kiểm tra user devops có thể ghi file

Chuyển sang user `devops`:

```bash
su - devops
```

Ghi thêm nội dung mà không dùng `sudo`:

```bash
echo "Update" >> /var/www/ptit-web/html/index.html
```

Kiểm tra nội dung:

```bash
cat /var/www/ptit-web/html/index.html
```

Kết quả:

```text
<h1>PTIT Web - Bài 5</h1>
Update
```

## 8. Cấu hình Nginx

Nginx sử dụng thư mục:

```nginx
root /var/www/ptit-web/html;
```

Kiểm tra:

```bash
grep "root " /etc/nginx/sites-enabled/default
```

Kết quả:

```text
root /var/www/ptit-web/html;
```

Kiểm tra cấu hình Nginx:

```bash
nginx -t
```

Kết quả:

```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Reload Nginx:

```bash
systemctl reload nginx
```

## 9. Kết luận

Đã hoàn thành yêu cầu Bài 5:

- `devops` là owner của `/var/www/ptit-web/`.
- `www-data` là group để Nginx có thể đọc website.
- `devops` có thể ghi `index.html` mà không cần `sudo`.
- Nginx được cấu hình sử dụng `/var/www/ptit-web/html`.
