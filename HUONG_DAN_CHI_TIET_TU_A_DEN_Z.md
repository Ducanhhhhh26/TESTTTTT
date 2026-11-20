# 📘 HƯỚNG DẪN THỰC HIỆN CHI TIẾT TỪ A ĐẾN Z - BÀI THI DEVOPS FINAL
## ĐỀ 001: HỆ THỐNG QUẢN LÝ PHÒNG LAB (100/100 ĐIỂM)

> Tài liệu này được đúc kết toàn diện, chuẩn hóa từng câu lệnh và file cấu hình để bạn có thể **làm lại từ đầu hoặc áp dụng ngay cho bất kỳ đề thi tương tự nào** một cách nhanh chóng và chính xác tuyệt đối.

---

## 📌 PHẦN 1: BẢNG THÔNG TIN SINH VIÊN & THÔNG SỐ TRIỂN KHAI

| Thông số | Giá trị thực tế | Ghi chú / Quy tắc |
| :--- | :--- | :--- |
| **Họ và tên** | **Nguyễn Đức Anh** | Điền họ và tên có dấu hoặc không dấu |
| **Mã sinh viên** | **B24DTCN184** | Viết hoa mã sinh viên |
| **Lớp** | **K24CNTT2** | Lớp hành chính |
| **Email sinh viên** | `ducanhcualo2403@gmail.com` | Email cá nhân |
| **GitHub Username** | `Ducanhhhhh26` | Tài khoản GitHub cá nhân |
| **Mã đề thi** | `001` | **KHÔNG** sửa biến `EXAM_CODE` |
| **Tên đề tài** | Quản lý phòng Lab | Hiển thị dữ liệu JSON phòng Lab |
| **GitHub Repo** | `https://github.com/Ducanhhhhh26/TESTTTTT.git` | Phải để chế độ **Public**, nhánh `main` |
| **Server Triển khai (VM)** | `20.6.130.18` | IP máy chủ chấm thi |
| **Tài khoản SSH Server** | `danh` | User được cấp trên máy ảo |
| **Cổng cá nhân (`APP_PORT`)** | `8081` | Từ 8080 trở lên, không trùng với bạn khác |
| **Tên Compose Project** | `ducanh-de001` | Cú pháp: `<tên-viết-tắt>-de<mã-đề>` |
| **URL Website** | `http://20.6.130.18:8081` | Tự động chuyển hướng sang API |
| **URL API kiểm tra** | `http://20.6.130.18:8081/api/labs` | Trả về chuỗi JSON 8 bản ghi phòng lab |

---

## 📊 PHẦN 2: BẢNG MA TRẬN ĐỐI CHIẾU THANG ĐIỂM ĐỀ BÀI (100 ĐIỂM)

| Phần thi | Điểm | File / Vị trí phụ trách | Yêu cầu kỹ thuật đạt điểm tối đa |
| :--- | :---: | :--- | :--- |
| **Phần 1: Khởi tạo repo & điền thông tin** | **10** | `student.env` | Repo Public, nhánh `main`, điền 100% biến không bỏ trống ô nào. |
| **Phần 2: Dockerfile Multi-stage** | **25** | `Dockerfile` | Stage 1: Gradle 8.14 cache layer `dependencies`, build `bootJar`.<br>Stage 2: Temurin JRE 21, user `spring` (non-root), `EXPOSE 8080`. |
| **Phần 3: Docker Compose** | **20** | `docker-compose.yml`<br>`nginx/default.conf` | Đầy đủ 3 service (`mysql`, `app`, `nginx`), ánh xạ cổng `8081:80`, mạng riêng `db-net`/`proxy-net`, `condition: service_healthy`. |
| **Phần 4: Pipeline CI/CD GitHub Actions** | **35** | `.github/workflows/ci-cd.yml`<br>GitHub Secrets | Cấu hình SSH Key, Job `build` push GHCR 2 tag (`sha` và `latest`), Job `deploy` SSH tự động kéo image mới và chạy không build lại. |
| **Phần 5: Nộp minh chứng** | **10** | `screenshots/`<br>`submission-content/` | Chụp đủ 4 ảnh: `01-actions.png`, `02-ghcr.png`, `03-compose-ps.png`, `04-website.png`. |

---

## 🚀 PHẦN 3: QUY TRÌNH THỰC HIỆN TỪ ĐẦU (STEP-BY-STEP WORKFLOW)

```mermaid
flowchart TD
    A["1. Khởi tạo Repo GitHub & điền student.env"] --> B["2. Viết Dockerfile Multi-stage"]
    B --> C["3. Hoàn thiện docker-compose.yml & Nginx"]
    C --> D["4. Cấu hình SSH Key & GitHub Secrets"]
    D --> E["5. Viết Pipeline CI/CD và Push Git"]
    E --> F["6. Kiểm tra Container & Website thực tế"]
    F --> G["7. Chụp 4 ảnh minh chứng & Nén nộp RAIA"]
```

### Bước 1: Khởi tạo Repository trên GitHub
1. Vào GitHub cá nhân, tạo một Repository mới ở chế độ **Public**: `TESTTTTT`.
2. Khởi tạo mã nguồn và đẩy lên nhánh `main`:
   ```bash
   git init
   git add .
   git commit -m "feat: initial commit for devops final de001"
   git branch -M main
   git remote add origin https://github.com/Ducanhhhhh26/TESTTTTT.git
   git push -u origin main
   ```

---

### Bước 2: Hoàn thiện File `student.env`
Mở file `student.env` và điền đầy đủ các giá trị (không được để trống bất kỳ trường nào):

```properties
# =============================================================================
#  student.env - THÔNG TIN SINH VIÊN & THÔNG SỐ TRIỂN KHAI (ĐỀ 001)
# =============================================================================

# ---------- 1. Thông tin sinh viên ----------
STUDENT_NAME="Nguyễn Đức Anh"
STUDENT_ID="B24DTCN184"
STUDENT_CLASS="K24CNTT2"
STUDENT_EMAIL="ducanhcualo2403@gmail.com"
GITHUB_USERNAME="Ducanhhhhh26"

# ---------- 2. Thông số triển khai trên server chung ----------
EXAM_CODE="001"
APP_PORT="8081"
COMPOSE_PROJECT_NAME="ducanh-de001"

# ---------- 3. Cơ sở dữ liệu MySQL ----------
MYSQL_DATABASE="ptit_lab_db"
MYSQL_USER="app_user"
MYSQL_PASSWORD="AppPass@2026"
MYSQL_ROOT_PASSWORD="RootPass@2026"
```

---

### Bước 3: Hoàn thiện File `Dockerfile` (Multi-stage Build)
Mở file `Dockerfile` ở thư mục gốc và cấu hình như sau:

```dockerfile
# syntax=docker/dockerfile:1
# =============================================================================
#  Dockerfile (multi-stage build cho Spring Boot 4 / Java 21 / Gradle)
# =============================================================================

# ---------- Stage 1: build - biên dịch & đóng gói bằng Gradle + JDK 21 ----------
FROM gradle:8.14-jdk21-alpine AS build
WORKDIR /workspace

# Copy build.gradle + settings.gradle trước để tận dụng Docker cache layer
COPY build.gradle settings.gradle ./
RUN gradle dependencies --no-daemon -q

# Copy mã nguồn và đóng gói JAR
COPY src ./src
RUN gradle bootJar -x test --no-daemon -q

# ---------- Stage 2: runtime - chỉ chứa JRE 21 + file JAR ----------
FROM eclipse-temurin:21-jre-alpine AS runtime

ARG APP_VERSION=dev
ENV APP_VERSION=${APP_VERSION} \
    JAVA_TOOL_OPTIONS="-XX:MaxRAMPercentage=75"

WORKDIR /app

# Tạo user thường non-root để đảm bảo an ninh
RUN addgroup -S spring && adduser -S spring -G spring
COPY --from=build --chown=spring:spring /workspace/build/libs/app.jar /app/app.jar
USER spring

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

---

### Bước 4: Hoàn thiện File `docker-compose.yml`
Mở file `docker-compose.yml` và điền đầy đủ các TODO 1 đến 5:

```yaml
# =============================================================================
#  docker-compose.yml - Quản lý phòng Lab (Đề 001)
# =============================================================================
name: ${COMPOSE_PROJECT_NAME:?Chua dien COMPOSE_PROJECT_NAME trong student.env}

services:
  # ---------------------------------------------------------------- MySQL
  mysql:
    image: mysql:8.0
    restart: unless-stopped
    command: ["--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci"]
    environment:
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      TZ: Asia/Ho_Chi_Minh
    volumes:
      - mysql_data:/var/lib/mysql
      - ./database/init.sql:/docker-entrypoint-initdb.d/01-init.sql:ro
    healthcheck:
      test: ["CMD-SHELL", "mysqladmin ping -h 127.0.0.1 -u$$MYSQL_USER -p$$MYSQL_PASSWORD --silent"]
      interval: 10s
      timeout: 5s
      retries: 12
      start_period: 30s
    networks:
      - db-net

  # ---------------------------------------------------------------- Spring Boot
  app:
    # TODO 1: build image từ thư mục gốc, truyền build-arg APP_VERSION
    build:
      context: .
      args:
        APP_VERSION: ${APP_VERSION:-local}
    # TODO 2: tên image = <APP_IMAGE>:<APP_TAG>
    image: ${APP_IMAGE:-devops-final-app}:${APP_TAG:-latest}
    restart: unless-stopped
    environment:
      DB_HOST: mysql
      DB_PORT: "3306"
      # TODO 3: lấy tên database, tài khoản, mật khẩu MySQL từ student.env
      DB_NAME: ${MYSQL_DATABASE}
      DB_USER: ${MYSQL_USER}
      DB_PASSWORD: ${MYSQL_PASSWORD}
      TZ: Asia/Ho_Chi_Minh
    depends_on:
      mysql:
        condition: service_healthy
    networks:
      - db-net
      - proxy-net

  # ---------------------------------------------------------------- Nginx (reverse proxy)
  nginx:
    image: nginx:1.27-alpine
    restart: unless-stopped
    # TODO 4: publish cổng cá nhân APP_PORT (8081) ra cổng 80 của container
    ports:
      - "${APP_PORT}:80"
    # TODO 5: mount cấu hình read-only
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - app
    networks:
      - proxy-net

networks:
  proxy-net:
  db-net:

volumes:
  mysql_data:
```

---

### Bước 5: Cấu hình SSH Key & GitHub Secrets (Phần 4.1)

#### 1. Tạo cặp khóa SSH trên máy thật hoặc máy ảo:
```bash
ssh-keygen -t ed25519 -f deploy_key -N ""
```
Lệnh trên sẽ tạo ra 2 file:
- `deploy_key`: Đây là **Private Key** (nội dung dùng để điền vào Secret trên GitHub).
- `deploy_key.pub`: Đây là **Public Key** (đưa vào file `authorized_keys` trên máy ảo).

#### 2. Thêm Public Key vào server (trên máy ảo chạy tài khoản `danh`):
```bash
# Tạo thư mục .ssh nếu chưa có và gán quyền chuẩn
mkdir -p ~/.ssh
chmod 700 ~/.ssh

# Đưa public key vào authorized_keys
cat deploy_key.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
chmod 755 /home/danh
```

#### 3. Cài đặt 3 Secrets trên GitHub Repository:
Vào GitHub: **Settings** -> **Secrets and variables** -> **Actions** -> **New repository secret**:
1. `SERVER_HOST`: `20.6.130.18`
2. `SERVER_USER`: `danh`
3. `SERVER_SSH_KEY`: Copy toàn bộ nội dung file private key `deploy_key` (bắt đầu bằng `-----BEGIN OPENSSH PRIVATE KEY-----` và kết thúc bằng `-----END OPENSSH PRIVATE KEY-----`).

---

### Bước 6: Hoàn thiện File `.github/workflows/ci-cd.yml`
Mở file `.github/workflows/ci-cd.yml` và hoàn thiện đầy đủ TODO 1–6:

```yaml
name: CI/CD

on:
  push:
    branches: [main]
  workflow_dispatch:

env:
  REGISTRY: ghcr.io

jobs:
  build:
    name: Build & Test
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    outputs:
      image: ${{ steps.meta.outputs.image }}
    steps:
      - name: Checkout mã nguồn
        uses: actions/checkout@v4

      - name: Kiểm tra thông tin trong student.env
        run: |
          set -a; source student.env; set +a
          for v in STUDENT_NAME STUDENT_ID STUDENT_CLASS STUDENT_EMAIL GITHUB_USERNAME \
                   APP_PORT COMPOSE_PROJECT_NAME MYSQL_USER MYSQL_PASSWORD MYSQL_ROOT_PASSWORD; do
            if [ -z "${!v}" ]; then
              echo "::error file=student.env::Chưa điền $v trong student.env"
              exit 1
            fi
          done
          if [ "$APP_PORT" -lt 8080 ]; then
            echo "::error file=student.env::APP_PORT phải từ 8080 trở lên"
            exit 1
          fi
          echo "### Sinh viên: $STUDENT_NAME - $STUDENT_ID - $STUDENT_CLASS" >> "$GITHUB_STEP_SUMMARY"

      - name: Đặt tên image
        id: meta
        run: echo "image=${REGISTRY}/${GITHUB_REPOSITORY,,}/app" >> "$GITHUB_OUTPUT"

      - name: Build & chạy thử bằng Docker Compose
        env:
          APP_IMAGE: ${{ steps.meta.outputs.image }}
          APP_TAG: ${{ github.sha }}
          APP_VERSION: ${{ github.sha }}
        # TODO 1: build và khởi động bằng Docker Compose có wait
        run: docker compose --env-file student.env up -d --build --wait --wait-timeout 300

      - name: Smoke test
        run: |
          set -a; source student.env; set +a
          BASE="http://localhost:${APP_PORT}"
          curl -fsS --retry 12 --retry-delay 5 --retry-all-errors "$BASE/api/labs" | grep -q '"id"'
          echo "Smoke test OK"

      - name: In log khi lỗi
        if: failure()
        run: docker compose --env-file student.env logs --no-color

      # TODO 2: đăng nhập GHCR
      - name: Đăng nhập GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      # TODO 3: push image với 2 tag commit SHA và latest
      - name: Push image lên GHCR
        env:
          IMAGE: ${{ steps.meta.outputs.image }}
        run: |
          docker push "$IMAGE:${{ github.sha }}"
          docker tag "$IMAGE:${{ github.sha }}" "$IMAGE:latest"
          docker push "$IMAGE:latest"

      - name: Dọn dẹp
        if: always()
        run: docker compose --env-file student.env down -v

  deploy:
    name: Deploy lên server
    # TODO 4: chỉ chạy khi job build thành công
    needs: build
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: read
    steps:
      - name: Deploy qua SSH
        uses: appleboy/ssh-action@v1.2.0
        env:
          APP_IMAGE: ${{ needs.build.outputs.image }}
          APP_TAG: ${{ github.sha }}
          GHCR_USER: ${{ github.actor }}
          GHCR_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          REPO_URL: ${{ github.server_url }}/${{ github.repository }}.git
          DEPLOY_DIR: ${{ github.event.repository.name }}
        with:
          # TODO 5: kết nối bằng secrets
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SERVER_SSH_KEY }}
          envs: APP_IMAGE,APP_TAG,GHCR_USER,GHCR_TOKEN,REPO_URL,DEPLOY_DIR
          script: |
            set -e
            if [ ! -d "$HOME/$DEPLOY_DIR/.git" ]; then
              git clone "$REPO_URL" "$HOME/$DEPLOY_DIR"
            fi
            cd "$HOME/$DEPLOY_DIR"
            git fetch origin main
            git reset --hard origin/main
            echo "$GHCR_TOKEN" | docker login ghcr.io -u "$GHCR_USER" --password-stdin
            export APP_IMAGE APP_TAG
            # TODO 6: pull image và khởi chạy không build lại
            docker compose --env-file student.env pull app
            docker compose --env-file student.env up -d --no-build --wait --wait-timeout 300
            docker logout ghcr.io
            docker compose --env-file student.env ps
```

---

### Bước 7: Thực hiện Chạy & Kiểm thử Thực tế trên Máy Ảo

Mở Terminal SSH vào máy ảo (`danh@Server:~$`) và thao tác:

```bash
# 1. Phân quyền Docker socket (chỉ cần chạy 1 lần nếu gặp lỗi permission denied):
sudo usermod -aG docker $USER
sudo chmod 666 /var/run/docker.sock

# 2. Đồng bộ mã nguồn chuẩn repo TESTTTTT:
# Cách chuẩn và sạch sẽ nhất (tránh lỗi thiếu file student.env):
cd ~
rm -rf TESTTTTT
git clone https://github.com/Ducanhhhhh26/TESTTTTT.git
cd TESTTTTT

# (Hoặc nếu đang đứng trong ~/TESTTTTT, chạy lệnh này để cập nhật mã nguồn mới nhất):
# git fetch origin main && git reset --hard origin/main

# 3. Khởi động các container:
docker compose --env-file student.env up -d --build

# 4. Kiểm tra trạng thái các container:
docker compose --env-file student.env ps

# 5. Kiểm tra API nội bộ trả về JSON:
curl http://localhost:8081/api/labs
```

---

### Bước 8: Thu Thập 4 Ảnh Minh Chứng Nộp Bài (Phần 5 - 10 Điểm)

Chụp và lưu 4 tệp ảnh vào cả 2 thư mục `screenshots/` và `submission-content/`:

1. **`01-actions.png`**: Chụp giao diện GitHub Actions hiển thị lần chạy mới nhất trên nhánh `main` với cả 2 job **Build & Test** và **Deploy lên server** đều xanh ✅.
2. **`02-ghcr.png`**: Chụp giao diện GitHub Packages hiển thị package `app` với 2 tag: **`latest`** và commit SHA.
3. **`03-compose-ps.png`**: Chụp Terminal máy ảo lệnh `docker compose --env-file student.env ps` hiển thị 3 service `ducanh-de001-app`, `ducanh-de001-mysql`, `ducanh-de001-nginx` đều `Up`.
4. **`04-website.png`**: Chụp trình duyệt web trên máy tính thật truy cập địa chỉ `http://20.6.130.18:8081/api/labs` trả về đầy đủ dữ liệu JSON.

---

### Bước 9: Quy Cách Đóng Gói và Nộp Bài

1. Đổi tên thư mục gốc của bài làm thành:
   ```text
   devops-final-de001-nguyenducanh
   ```
2. Nén thành file zip:
   ```text
   devops-final-de001-nguyenducanh.zip
   ```
3. Nộp file zip lên cổng nộp bài RAIA.

---

## 🛠️ PHẦN 4: CÁC LỖI THƯỜNG GẶP VÀ CÁCH XỬ LÝ NHANH

| Hiện tượng lỗi | Nguyên nhân | Cách khắc phục ngay lập tức |
| :--- | :--- | :--- |
| `couldn't find env file: .../student.env` | Thư mục repo trên máy ảo bị cũ hoặc chưa kéo đủ file mã nguồn | Chạy: `cd ~/TESTTTTT && git fetch origin main && git reset --hard origin/main` hoặc `cd ~ && rm -rf TESTTTTT && git clone https://github.com/Ducanhhhhh26/TESTTTTT.git` |
| `permission denied while trying to connect to Docker daemon socket` | User `danh` chưa có quyền đọc ghi socket Docker | Chạy lệnh: `sudo chmod 666 /var/run/docker.sock` |
| `curl: (56) Recv failure: Connection reset by peer` | Container `app` chưa khởi động xong hoặc chưa healthy | Chờ 15–20 giây cho MySQL khởi tạo bảng xong hoặc kiểm tra `docker compose logs app` |
| GitHub Action SSH fail ở bước `drone-ssh` | Sai SSH Key hoặc file `authorized_keys` sai quyền | Kiểm tra `chmod 600 ~/.ssh/authorized_keys` và `chmod 755 /home/danh` |
| GHCR `denied: installation not allowed to Create organization package` | Quyền token chưa được bật gói Packages | Vào Settings Repo -> Actions -> General -> Workflow permissions -> Chọn **Read and write permissions** |
| Docker Desktop trên Windows không thấy container nào | Container đang chạy trên Server VM Linux chứ không phải Windows | Hoàn toàn bình thường, kiểm tra container qua Terminal máy ảo bằng `docker compose ps` |
