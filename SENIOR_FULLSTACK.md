# โจทย์ข้อสอบ Senior Full Stack: Architecture Design Document

## บริบท (Context)

บริษัทต้องการพัฒนาระบบจอง **"ตั๋วจับมือไอดอล"** — งาน Handshake Event ที่มีไอดอล 50 คน แต่ละคนมีช่วงเวลาจองจำกัด โดยมีแฟนคลับประมาณ **200,000 คน** เปิดเว็บรอกดจองพร้อมกันในเวลา **10:00:00 น. ตรง** ก่อให้เกิด Traffic Spike หนักมากในช่วงวินาทีแรก

ในฐานะ **Senior Full Stack Engineer** คุณต้องออกแบบระบบที่รับมือสถานการณ์นี้ได้ และเขียน Architecture Design Document (ADD) ที่ครอบคลุมและสมเหตุสมผล

> **หมายเหตุ:** โจทย์นี้วัดทักษะการ **ออกแบบและอธิบายระบบ** ไม่ใช่การ Implement จริง

---

## เงื่อนไขทางธุรกิจ (Business Requirements)

### 1. Booking & Inventory

- แต่ละคนจองได้ **1 ตั๋ว/ไอดอล/รอบ** เท่านั้น
- เมื่อกดจองสำเร็จ ระบบต้อง **ล็อคตั๋ว 5 นาที** เพื่อรอการชำระเงินผ่าน Payment Gateway
- หากชำระเงินไม่สำเร็จภายใน 5 นาที ตั๋วต้อง **ถูกปล่อยคืนคลังทันที**
- **ห้ามขายเกินจำนวน (No Overselling)** เด็ดขาด

### 2. Accuracy

- หน้าเว็บต้องแสดงสถานะตั๋วแบบ Near Real-time: **ว่าง / ล็อค / หมด**
- ยอดเงินต้อง Reconcile กับจำนวนตั๋วที่ขายได้ **100%**

### 3. Backoffice Operations

- แอดมินต้องดูสถานะการจองของแต่ละคนได้ (เช่น รอชำระเงิน / ชำระแล้ว / ล็อคหมดเวลา)
- การใช้งาน Backoffice **ต้องไม่กระทบ** Performance ของระบบจองหลัก

---

## สิ่งที่ต้องส่งมอบ (Deliverables)

เขียน Architecture Design Document (ADD) พร้อมวาด Diagram ครอบคลุมประเด็นต่อไปนี้:

### 1. Core Architecture (Frontend & Backend)

- ภาพรวม System Architecture สำหรับรับมือ Traffic Spike จากผู้ใช้ 200,000 คนพร้อมกัน
- กลยุทธ์ฝั่ง Client (Frontend) ในการจัดการ High Load และมอบ UX ที่ดี

### 2. Concurrency & Inventory Management

- การออกแบบ Database / Cache Layer เพื่อป้องกัน Race Condition และ Overselling
- กลไกการล็อคตั๋ว 5 นาที และการปล่อยตั๋วคืนอัตโนมัติเมื่อหมดเวลา

### 3. Payment & Edge Cases

- วิธีจัดการกรณี Payment Gateway
- Business Logic ในการ Reconcile และ Handle กรณีนี้ควรเป็นอย่างไร?

### 4. Backoffice Architecture

- ออกแบบระบบหลังบ้านให้แอดมิน Query และดูสถานะการจองของแต่ละคนได้
- **แยก Resource** จากระบบจองหลักอย่างไร เพื่อไม่ให้กระทบ Performance
