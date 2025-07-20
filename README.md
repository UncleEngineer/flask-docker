# flask-docker
flask-docker config

Flask Docker Deployment


1. เตรียม Flask Project Local พร้อม Dockerfile และ docker-compose

my-flask-app/
│
├── app/
│   ├── __init__.py
│   └── app.py
├── Dockerfile
├── docker-compose.yml
└── requirements.txt

1.1 ตัวอย่าง app/app.py

from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return "Hello from Flask + Docker!"

1.2 requirements.txt
Flask
gunicorn

1.3 Dockerfile
—------
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["gunicorn", "-b", "0.0.0.0:5000", "app.app:app"]

—------

1.4 docker-compose.yml
—-----
version: '3'

services:
  web:
    build: .
    ports:
      - "80:5000"
    restart: always

—-----
2. ทดสอบ Local
docker-compose up --build
เปิด browser: http://localhost  ถ้ารันได้แปลว่า Docker Compose setup ถูกต้องแล้ว

3. เตรียม Droplet DigitalOcean
-สร้าง Droplet (Ubuntu LTS เช่น 22.04)
-เพิ่ม SSH Key หรือ Password ก็ได้
-SSH เข้าเครื่อง

4. ติดตั้ง Docker + Docker Compose บน DigitalOcean

# ติดตั้ง Docker
apt update && apt install docker.io -y

# ติดตั้ง Docker Compose
apt install docker-compose -y

# ตรวจสอบเวอร์ชัน
docker -v
docker-compose -v

5. อัปโหลดโค้ดไปยัง Droplet
วิธีที่ 1: clone git
apt install git -y
git clone https://github.com/youruser/my-flask-app.git
cd my-flask-app


วิธีที่ 2: scp
scp -r my-flask-app root@your_ip:/root/

6. สั่งรัน Production ด้วย Docker Compose

cd my-flask-app
docker-compose up -d --build
เปิดเบราว์เซอร์ http://your_droplet_ip จะเห็นหน้า Flask แล้ว

ถ้ามีการเปลี่ยนแปลงโค้ด

1. อัพโหลดไฟล์ใหม่
scp -r my-flask-app root@your_ip:/root/
2. SSH ไป server แล้วสั่ง

docker-compose down
docker-compose up -d --build

สามารถใช้ docker-compose up -d --build โดย ไม่ต้อง down ก่อน ระบบจะ build ใหม่แล้ว restart container โดยไม่หยุดเซอร์วิสทันที

