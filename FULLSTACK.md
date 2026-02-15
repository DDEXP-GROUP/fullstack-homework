# การบ้าน Full Stack: ระบบอัปโหลดรูปภาพ

## โจทย์

สร้างเว็บแอปพลิเคชันแบบ Full Stack (Frontend + Backend) สำหรับให้ผู้ใช้อัปโหลดรูปภาพไปจัดเก็บบนเครื่อง Local หรือ Cloud Storage

## สิ่งที่ต้องทำ

### Backend

- [ ] สร้าง REST API สำหรับรับอัปโหลดรูปภาพ
- [ ] จัดเก็บรูปภาพบนเครื่อง Local ของท่านหรือ Cloud Storage (เช่น AWS S3, Google Cloud Storage)
- [ ] รองรับไฟล์รูปภาพ: JPEG, PNG, WebP
- [ ] ส่ง Response กลับพร้อม URL ของรูปภาพที่อัปโหลดสำเร็จ
- [ ] จัดการ Error ให้ครอบคลุม:
  - ไฟล์ที่ส่งมาไม่ใช่รูปภาพ หรือเป็นรูปแบบที่ไม่รองรับ
  - ขนาดไฟล์เกิน 5MB
  - จัดเก็บรูปภาพไม่สำเร็จ

> **หมายเหตุ:** ไม่บังคับรูปแบบ API Response — ออกแบบโครงสร้าง Request/Response ได้อิสระตามความเหมาะสม

### Frontend

ดูดีไซน์ได้ที่: [Figma](https://www.figma.com/design/ylh1Vx3GYzzSSS9WTIv869/Full-Stack-Homework?node-id=0-1&t=MhYOB9jyCes110xz-1)

- [ ] สร้างหน้าจออัปโหลด
- [ ] แสดงตัวอย่างรูปภาพได้ในขณะอัปโหลด
- [ ] แสดง URL หรือตัวอย่างรูปภาพหลังอัปโหลดสำเร็จ

## คะแนนพิเศษ (ทำได้ยิ่งดี แต่ไม่บังคับ)

- [ ] **อัปโหลดหลายไฟล์พร้อมกัน**
- [ ] **Link รูปภาพมีวันหมดอายุ** — สามารถกำหนดระยะเวลาที่ Link ใช้งานได้
- [ ] **Testing** - มี Unit Test หรือ Integration Test
- [ ] **Deployment** - สามารถ Deploy ได้และใช้งานได้จริง

## สำหรับผู้สมัครระดับ Senior

นอกจากทำตามข้อกำหนดด้านบนแล้ว จะพิจารณาเพิ่มเติมในเรื่อง:

- **Code Structure** — โครงสร้างโค้ดเป็นระเบียบ แยก Layer ชัดเจน อ่านง่าย ดูแลต่อได้
- **Performance** — การจัดการ Resource อย่างมีประสิทธิภาพทั้ง Frontend และ Backend
- **Security** — Validate Input ทั้ง Client และ Server Side เป็นต้น

---

## เทคโนโลยีที่ใช้

เลือกใช้เทคโนโลยีอะไรก็ได้ตามถนัด ตัวอย่างที่แนะนำ:

| ส่วน       | ตัวอย่าง                                                |
| ---------- | ------------------------------------------------------- |
| Frontend   | React, Next.js, Vue, Nuxt, Svelte, Angular, Remix, Astro |
| Backend    | Node.js (Express/Fastify/NestJS), Go (Gin/Fiber), Python (FastAPI/Django), Bun.js (Hono/Elysia) |
| Storage    | AWS S3, Google Cloud Storage, Cloudinary, MinIO (local), Azure Blob Storage, Supabase Storage, Cloudflare R2 |

## วิธีส่งงาน

1. สร้าง GitHub Repository ใหม่ของตัวเอง
2. พัฒนาตามข้อกำหนดด้านบน
3. นำลิงก์ Repository ส่งกลับมาที่ team@ddexpgroup.com

## มีคำถาม?

หากมีข้อสงสัยเกี่ยวกับโจทย์ สามารถส่งอีเมลกลับมาสอบถามได้ที่ team@ddexpgroup.com
