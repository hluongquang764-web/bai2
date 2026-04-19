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

<img width="982" height="662" alt="image" src="https://github.com/user-attachments/assets/fad8bd65-c143-4d00-a06c-c40ce7405b2e" />


---

# 5. Đăng nhập File Browser lần đầu

Password được tạo tự động khi khởi động, xem bằng lệnh:

```bash
docker logs myfilebrowser | grep password
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/79311d06-fe54-446d-9d2a-5afd4e5c7329" />


---
# 6. File Browser hoạt động qua Nginx

Sau khi cấu hình Nginx proxy `/files` tới container `myfilebrowser`, truy cập `http://172.21.208.37/files` để kiểm tra.

Nginx đóng vai trò trung gian, nhận request từ người dùng tại `/files` rồi chuyển tiếp tới File Browser đang chạy trong container. Nhờ đó người dùng không cần biết port 8080 mà vẫn truy cập được File Browser.

```bash
docker restart mynginx
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b72fc274-bf53-447a-ad70-f092e5b22ded" />

---

# 7. Cho phép người dùng đăng ký

Vào **Cài đặt** → **Cài đặt chung** → tích **Cho phép người dùng đăng ký** → **CẬP NHẬT**.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/089a977e-851d-4608-9f6d-bf9ec1acefac" />

---

# 8. File Browser public qua domain

Truy cập `https://luongquangha.io.vn/files`

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/94d7fd5e-ff02-467d-b3bc-be7af15cda16" />

Sau khi đăng nhập

<img width="1920" height="1077" alt="image" src="https://github.com/user-attachments/assets/e81407bb-59ff-4b09-b8bc-ecfa3a932d64" />

---

# 9. Trang đăng ký tài khoản

Người dùng tự đăng ký tại `https://luongquangha.io.vn/files`

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/019c5281-5229-4ab7-9567-c055a60310f1" />


---


---

# 10. Hướng dẫn sử dụng File Browser

## 10.1. Upload file
Nhấn nút **Upload** (mũi tên lên) ở góc trên phải → chọn file từ máy tính → file sẽ được upload lên server.

![Upload file](images/10.png)

## 10.2. Tạo thư mục mới
Nhấn **Thư mục mới** ở menu bên trái → nhập tên thư mục → nhấn OK.

![Tạo thư mục](images/11.png)

## 10.3. Tạo file mới
Nhấn **Tập tin mới** ở menu bên trái → nhập tên file → nhấn OK → có thể chỉnh sửa nội dung trực tiếp.

![Tạo file mới](images/12.png)

## 10.4. Download file
Click vào file cần tải → nhấn nút **Download** (mũi tên xuống) ở góc trên phải.

![Download file](images/13.png)

## 10.5. Xóa file
Click vào file cần xóa → nhấn nút **Xóa** (thùng rác) → xác nhận.

![Xóa file](images/14.png)

## 10.6. Chia sẻ file
Click vào file → nhấn nút **Chia sẻ** → copy link chia sẻ để gửi cho người khác.

![Chia sẻ file](images/15.png)
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
