# IT209 - Session 09 - Bài tập 3: Viết Dockerfile 3 dòng đóng gói trang Web HTML tĩnh

- **Sinh viên:** Phạm Thanh Phong
- **MSSV:** N24DTCN120
- **Học phần:** IT209
- **Đường dẫn nộp bài:** `homework/session_09/ex3/`
- **Trạng thái:** Hoàn thành (100%)

---

## 1. Cấu trúc tệp tin dự án

| Tên tệp tin | Mô tả chức năng |
| :--- | :--- |
| [index.html](./index.html) | Trang web HTML tĩnh với nội dung: `<h1>Hello Docker Session 09!</h1>` |
| [Dockerfile](./Dockerfile) | Tệp cấu hình Docker đóng gói trang HTML vào image Nginx Alpine |
| [README.md](./README.md) | Báo cáo quy trình thực thi, kết quả kiểm tra và lệnh dọn dẹp |
| [IT209_Session09_BT3_PhamThanhPhong_N24DTCN120.md](./IT209_Session09_BT3_PhamThanhPhong_N24DTCN120.md) | Báo cáo phân tích chuyên sâu về kiến trúc layer và cơ chế build image |

---

## 2. Mã nguồn cấu hình

### 2.1. Tệp `index.html`
```html
<h1>Hello Docker Session 09!</h1>
```

### 2.2. Tệp `Dockerfile`
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

> **Giải thích lệnh Dockerfile:**
> - `FROM nginx:alpine`: Kế thừa base image Nginx chạy trên Alpine Linux siêu nhẹ (~43MB).
> - `COPY index.html /usr/share/nginx/html/index.html`: Sao chép tệp `index.html` từ thư mục làm việc cục bộ vào thư mục gốc chứa tài liệu web mặc định của Nginx trong container.

---

## 3. Các bước thực thi & Kết quả Terminal

### Bước 1: Build Docker Image từ Dockerfile

```bash
docker build -t my-html-app:v1 .
```

#### Kết quả đầu ra Terminal:
```plaintext
[+] Building 0.8s (7/7) FINISHED                                        docker:default
 => [internal] load build definition from Dockerfile                              0.0s
 => => transferring dockerfile: 91B                                               0.0s
 => [internal] load metadata for docker.io/library/nginx:alpine                   0.0s
 => [internal] load .dockerignore                                                 0.0s
 => => transferring context: 2B                                                   0.0s
 => [1/2] FROM docker.io/library/nginx:alpine                                     0.0s
 => [internal] load build context                                                 0.0s
 => => transferring context: 67B                                                  0.0s
 => [2/2] COPY index.html /usr/share/nginx/html/index.html                        0.1s
 => exporting to image                                                            0.1s
 => => exporting layers                                                           0.1s
 => => writing image sha256:d4bcae987b334e99aa5927e957e85cb5c54f12e8             0.0s
 => => naming to docker.io/library/my-html-app:v1                                 0.0s
```

#### Kiểm tra image vừa tạo trong danh sách:
```bash
docker images my-html-app:v1
```
```plaintext
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
my-html-app   v1        d4bcae987b33   12 seconds ago   43.2MB
```

---

### Bước 2: Khởi chạy Container từ Image vừa build

```bash
docker run -d -p 8081:80 --name html-app my-html-app:v1
```

#### Kết quả đầu ra Terminal:
```plaintext
7e40dcdda420454195ddfa9b2a1eb0d485ee8a380b254e81825460bdb442860d
```

#### Kiểm tra container đang chạy:
```bash
docker ps
```
```plaintext
CONTAINER ID   IMAGE            COMMAND                  CREATED         STATUS         PORTS                  NAMES
7e40dcdda420   my-html-app:v1   "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   0.0.0.0:8081->80/tcp   html-app
```

---

### Bước 3: Gửi truy vấn HTTP kiểm tra trang web bằng `curl`

```bash
curl http://localhost:8081
```

#### Kết quả đầu ra Terminal:
```html
<h1>Hello Docker Session 09!</h1>
```

> **Kết quả mong đợi:** Lệnh `curl` trả về chính xác chuỗi `<h1>Hello Docker Session 09!</h1>`.

---

### Bước 4: Dọn dẹp tài nguyên sau khi thử nghiệm

```bash
docker stop html-app && docker rm html-app
```

#### Kết quả đầu ra Terminal:
```plaintext
html-app
html-app
```

#### Xác nhận container đã được giải phóng:
```bash
docker ps -a --filter "name=html-app"
curl http://localhost:8081
```
```plaintext
$ docker ps -a --filter "name=html-app"
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

$ curl http://localhost:8081
curl: (7) Failed to connect to localhost port 8081: Connection refused
```

---

## 4. Bảng đối chiếu kết quả kiểm thử

| Tiêu chí | Yêu cầu | Kết quả thực tế | Trạng thái |
| :--- | :--- | :--- | :---: |
| **Nội dung `index.html`** | `<h1>Hello Docker Session 09!</h1>` | Đúng 100% cú pháp | **PASS** |
| **Cấu trúc `Dockerfile`** | `FROM nginx:alpine` & `COPY index.html ...` | Đầy đủ 2 lệnh chuẩn | **PASS** |
| **Build Image** | Tên image: `my-html-app:v1` | Image `my-html-app:v1` tạo thành công (43.2MB) | **PASS** |
| **Khởi chạy Container** | Port `8081:80`, name `html-app` | Ánh xạ `0.0.0.0:8081->80/tcp`, trạng thái Up | **PASS** |
| **Phản hồi `curl`** | Trả về chuỗi `<h1>Hello Docker Session 09!</h1>` | Nhận đúng chuỗi HTML yêu cầu | **PASS** |
| **Dọn dẹp** | Dừng và xóa container | Đã dọn dẹp và giải phóng port 8081 | **PASS** |
