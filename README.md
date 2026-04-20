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

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/50eefb68-b6df-4b95-8516-eadab1773311" />


## 10.2. Tạo thư mục mới
Nhấn **Thư mục mới** ở menu bên trái → nhập tên thư mục → nhấn OK.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/942f13fb-7f5f-4184-b7fe-11ef8a1576b0" />


## 10.3. Tạo file mới
Nhấn **Tập tin mới** ở menu bên trái → nhập tên file → nhấn OK → có thể chỉnh sửa nội dung trực tiếp.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/90d06c93-3ece-4b13-9b2b-1a64000b6083" />

<img width="1918" height="1080" alt="image" src="https://github.com/user-attachments/assets/ce6f5455-080d-4246-8684-4aa627029d24" />

<img width="1920" height="1077" alt="image" src="https://github.com/user-attachments/assets/9987fbbc-2b1f-4b85-9a02-b6d45be7e734" />


## 10.4. Download file
Click vào file cần tải → nhấn nút **Download** (mũi tên xuống) ở góc trên phải.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1a4a2128-c12d-4b9f-8bc6-8d21224e484d" />

## 10.5. Xóa file
Click vào file cần xóa → nhấn nút **Xóa** (thùng rác) → xác nhận.

<img width="1920" height="1079" alt="image" src="https://github.com/user-attachments/assets/8aa8e682-f6fc-4418-b396-5af12d54f27a" />


## 10.6. Chia sẻ file
Click vào file → nhấn nút **Chia sẻ** → copy link chia sẻ để gửi cho người khác.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6da10b88-2d27-4afd-8beb-2d6e623ad719" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d8aa8b18-43ca-454e-bea2-80f552f88ebc" />


---

# 11. Node-RED kết nối File Browser API

Node-RED đóng vai trò cầu nối, gọi API của File Browser để lấy danh sách file, sau đó trả về cho trang web hiển thị.

## Flow Node-RED: [GET] /api/files-list

Gồm 4 node được nối theo thứ tự:
- **http in** (GET /api/files-list) → nhận request từ trang web
- **http request** (GET http://myfilebrowser:80/api/resources/) → gọi API File Browser kèm token xác thực
- **function** → xử lý JSON, lọc ra tên file, kích thước, loại, ngày sửa
- **http response** → trả kết quả về cho trang web

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/717f5708-60e2-4c00-bd92-bf6123ad6633" />

Kết quả API trả về:

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2b4f7ef3-14eb-4d9c-a9a5-c06520a16b6e" />


---

# 12. Trang web hiển thị danh sách file

Trang web tại `https://luongquangha.io.vn` tự động gọi `/api/files-list` khi tải và hiển thị danh sách file theo thời gian thực.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5594e4b0-7d80-4ac0-8a7d-16eca55ab9b6" />


---

# 13. Phan quyen truy cap

File Browser ho tro phan quyen chi tiet cho tung user:

| User | Quan tri vien | Pham vi | Quyen |
|------|------|------|------|
| admin | Co | Toan bo (.) | Toan quyen |
| nguyentienduc | Khong | /h | Chi tai xuong |
| luongha | Khong | /thumuc | Chi tai xuong |

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2782d6fb-70c0-42a6-8836-98c0a73e8ce7" />


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
