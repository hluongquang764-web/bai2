# Bài tập 02 - File Browser + Node-RED

**Họ tên:** Lương Quang Hà  
**MSSV:** K225480106010  
**Lớp:** 58KTP  
**File Browser:** https://luongquangha.io.vn/files  

---

# 1. Thêm File Browser vào docker-compose.yml

Thêm service `myfilebrowser` vào file `docker-compose.yml`:

```yaml
myfilebrowser:
  image: filebrowser/filebrowser:latest
  container_name: myfilebrowser
  restart: unless-stopped
  ports:
    - "8080:80"
  volumes:
    - ./filebrowser/data:/srv
    - ./filebrowser/filebrowser.db:/database/filebrowser.db
  command: --baseurl /files
```

<img width="978" height="659" alt="image" src="https://github.com/user-attachments/assets/1561c9af-c296-408d-9bf4-59b35607e8e7" />


---

# 2. Tạo thư mục và khởi động File Browser

```bash
mkdir -p ~/myapp/filebrowser/data
touch ~/myapp/filebrowser/filebrowser.db
docker-compose up -d myfilebrowser
```

<img width="985" height="664" alt="image" src="https://github.com/user-attachments/assets/5d0b52b1-7b76-483c-8214-0e246997b6ef" />


---

# 3. Kiểm tra các container đang chạy

```bash
docker ps
```
<img width="977" height="659" alt="image" src="https://github.com/user-attachments/assets/32d0dc5b-db6c-4265-9a69-dd71bdd074b2" />


---

# 4. Cấu hình Nginx proxy /files

Sửa file `nginx/nginx.conf` thêm block proxy cho File Browser:

```nginx
location ~* ^/files {
  proxy_pass http://myfilebrowser:80;
  proxy_set_header Host $host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto $scheme;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
}
```

![nginx.conf](images/4.png)

---

# 5. Đăng nhập File Browser lần đầu

Password được tạo tự động khi khởi động, xem bằng lệnh:

```bash
docker logs myfilebrowser | grep password
```

![Đăng nhập](images/5.png)

---

# 6. File Browser hoạt động qua Nginx

Truy cập `http://172.21.208.37/files`

![File Browser qua Nginx](images/6.png)

---

# 7. Cho phép người dùng đăng ký

Vào **Cài đặt** → **Cài đặt chung** → tích **Cho phép người dùng đăng ký** → **CẬP NHẬT**.

![Allow signup](images/7.png)

---

# 8. File Browser public qua domain

Truy cập `https://luongquangha.io.vn/files`

![File Browser public](images/8.png)

---

# 9. Trang đăng ký tài khoản

Người dùng tự đăng ký tại `https://luongquangha.io.vn/files`

![Đăng ký](images/9.png)

---

# Cấu trúc thư mục

```
myapp/
├── docker-compose.yml
├── nginx/
│   └── nginx.conf
├── myweb/
│   └── index.html
├── nodered/
│   └── settings.js
└── filebrowser/
    ├── data/
    └── filebrowser.db
```
