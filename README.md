# ระบบจัดการสินค้า (Product Management System) - ict203-test-n8n

ระบบจัดการสินค้าขั้นสูงที่ออกแบบด้วยความเรียบหรู (Premium Design) โดยใช้ **Vue.js 3** ร่วมกับ **Bootstrap 5** และเชื่อมต่อระบบหลังบ้านผ่าน **n8n Webhooks** เพื่อความรวดเร็วและยืดหยุ่นในการจัดการข้อมูล

---

## 🚀 ฟีเจอร์หลัก (Key Features)
*   **Premium UI/UX**: ออกแบบด้วยสไตล์เรียบหรู (Elegant Style) พร้อม Micro-animations และสถิติภาพรวม (Statistics Overview)
*   **Fully Responsive**: รองรับการใช้งานทั้งบนคอมพิวเตอร์และอุปกรณ์เคลื่อนที่
*   **CRUD via n8n**: ระบบเพิ่ม-อ่าน-แก้ไข-ลบ ข้อมูลสินค้าผ่าน Webhook ของ n8n
*   **Error Handling**: มีระบบดักจับและแสดงข้อผิดพลาดจาก API ที่ชัดเจน (Capture `msg` จาก Error 400)

---

## 🛠 ข้อมูลทางเทคนิค (Tech Stack)
*   **Frontend**: Vue 3, Bootstrap 5, Axios, Bootstrap Icons
*   **Backend Interface**: n8n Webhook Endpoint (`localhost:5678`)
*   **Data Format (API)**:
    ```json
    {
      "pro_id": 2,
      "pro_name": "DELL Server",
      "pro_desc": "DELL Server Description",
      "pro_price": 100000,
      "pro_count": 20
    }
    ```

---

## 📦 การติดตั้งและเริ่มต้นใช้งาน (Installation)

### 1. ส่วนของ Frontend (Vue App)
1.  ติดตั้ง Dependencies:
    ```bash
    npm install
    ```
2.  รันระบบ Development Server:
    ```bash
    npm run serve
    ```
    *ระบบจะเปิดที่ [http://localhost:8080](http://localhost:8080)*

### 2. ส่วนของ Backend (n8n-server)
อยู่ในโฟลเดอร์ `/n8n-server`
1.  **รัน n8n ด้วย Docker**:
    ```bash
    cd n8n-server
    docker-compose up -d
    ```
2.  **การตั้งค่า Workflow**:
    *   เปิด n8n ที่ [http://localhost:5678](http://localhost:5678)
    *   นำเข้าไฟล์ (Import) `ICT203-Test.json` เข้าไปในระบบ n8n
    *   ตรวจสอบให้แน่ใจว่า Webhook ถูกตั้งค่าไปที่ URL: `http://localhost:5678/webhook/product`
    *   เปิดใช้งาน Workflow (Activate) ก่อนเริ่มต้นใช้งาน

---

## 📂 โครงสร้างโปรเจกต์ที่สำคัญ
*   `src/views/HomeView.vue`: หน้าจอจัดการสินค้าหลัก (Premium CRUD UI)
*   `src/main.js`: การตั้งค่าระบบหลักและนำเข้า Bootstrap
*   `n8n-server/ICT203-Test.json`: Workflow สำเร็จรูปสำหรับจัดการข้อมูลสินค้า
*   `n8n-server/docker-compose.yml`: ไฟล์สำหรับรัน n8n ผ่าน Docker

---

## 👤 เกี่ยวกับโปรเจกต์
โปรเจกต์นี้สร้างขึ้นเพื่อทดสอบและสาธิตการสร้างระบบ Full-stack ในรูปแบบ Low-code Backend (n8n) ร่วมกับ Modern Frontend Framework (Vue.js) โดยเน้นความสวยงามและการใช้งานที่ง่ายเป็นระเบียบ
