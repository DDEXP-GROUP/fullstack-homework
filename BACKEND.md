# การบ้าน Backend: ระบบอัปโหลดและจัดการรูปภาพ

## โจทย์

สร้าง REST API สำหรับระบบอัปโหลดและจัดการรูปภาพ ที่รองรับการจัดเก็บรูปภาพบนเครื่อง Local หรือ Cloud Storage และสามารถดึงรายการรูปภาพกลับมาแสดงได้ โดยต้องทำครบทั้ง 2 API และต้องมี Unit Test

---

## สิ่งที่ต้องทำ

- [ ] สร้าง REST API สำหรับรับอัปโหลดรูปภาพ
- [ ] สร้าง REST API สำหรับดึงรายการรูปภาพ (Pagination, Sorting, Search)
- [ ] จัดเก็บรูปภาพบนเครื่อง Local ของท่านหรือ Cloud Storage (เช่น AWS S3, Google Cloud Storage)
- [ ] รองรับไฟล์รูปภาพ: JPEG, PNG, WebP
- [ ] บันทึกข้อมูลรูปภาพลงฐานข้อมูล
- [ ] จัดการ Error ให้ครอบคลุม
- [ ] เขียน Unit Test **(บังคับ)**

---

## API Endpoints

### 1. อัปโหลดรูปภาพ

```
POST /api/images/upload
Content-Type: multipart/form-data
```

**Request:**

| Field | Type | จำเป็น | รายละเอียด                    |
| ----- | ---- | ------ | ----------------------------- |
| file  | File | ใช่    | ไฟล์รูปภาพ (JPEG, PNG, WebP) |

**Response สำเร็จ (200):**

```json
{
  "success": true,
  "data": {
    "id": "abc123",
    "url": "https://storage.example.com/images/abc123.jpg",
    "fileName": "photo.jpg",
    "size": 102400,
    "mimeType": "image/jpeg",
    "uploadedAt": "2025-01-01T00:00:00.000Z"
  }
}
```

**Response ผิดพลาด (4xx/5xx):**

```json
{
  "success": false,
  "error": {
    "code": "INVALID_FILE_TYPE",
    "message": "support file type is jpeg, png, webp"
  }
}
```

---

### 2. ดึงรายการรูปภาพ (Pagination, Search)

```
GET /api/images?page=1&limit=20&sort=uploadedAt&order=desc&search=photo
```

**Query Parameters:**

| Parameter | Type   | Default    | รายละเอียด                                |
| --------- | ------ | ---------- | ----------------------------------------- |
| page      | number | 1          | หน้าที่ต้องการ                             |
| limit     | number | 20         | จำนวนรายการต่อหน้า (สูงสุด 100)            |
| sort      | string | uploadedAt | เรียงตามฟิลด์: uploadedAt, fileName, size |
| order     | string | desc       | ลำดับ: asc หรือ desc                      |
| search    | string | -          | ค้นหาตามชื่อไฟล์                           |

**Response สำเร็จ (200):**

```json
{
  "success": true,
  "data": {
    "images": [
      {
        "id": "abc123",
        "url": "https://storage.example.com/images/abc123.png",
        "fileName": "photo.jpg",
        "size": 102400,
        "mimeType": "image/jpeg",
        "uploadedAt": "2025-01-01T00:00:00.000Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 150,
      "totalPages": 8
    }
  }
}
```

---

## Error Codes

| Code                  | HTTP Status | คำอธิบาย                               |
| --------------------- | ----------- | -------------------------------------- |
| INVALID_FILE_TYPE     | 400         | ไฟล์ที่ส่งมาไม่ใช่ JPEG, PNG หรือ WebP |
| FILE_TOO_LARGE        | 400         | ขนาดไฟล์เกิน 5MB                      |
| NO_FILE_PROVIDED      | 400         | ไม่ได้แนบไฟล์มาใน Request              |
| INVALID_QUERY_PARAM   | 400         | Query Parameter ไม่ถูกต้อง             |
| UPLOAD_FAILED         | 500         | จัดเก็บรูปภาพไม่สำเร็จ                 |
| INTERNAL_SERVER_ERROR | 500         | เกิดข้อผิดพลาดที่ไม่คาดคิด             |

---

## Unit Test (บังคับ)

ตัวอย่าง test cases ขั้นต่ำที่ควรมี:

**Upload API**

- `should upload a valid JPEG file successfully`
- `should return INVALID_FILE_TYPE when uploading a .txt file`
- `should return FILE_TOO_LARGE when file exceeds 5MB`
- `should return NO_FILE_PROVIDED when no file is attached`

**List API**

- `should return paginated list of images`
- `should return empty array when no images exist`
- `should sort images by uploadedAt in descending order`

> หากมี test cases เพิ่มเติมนอกเหนือจากนี้ จะได้รับคะแนนพิเศษ

---

## คะแนนพิเศษ (ทำได้ยิ่งดี แต่ไม่บังคับ)

- [ ] **Optimize รูปภาพ** — ลดขนาดและบีบอัดรูปภาพก่อนจัดเก็บ
- [ ] **อัปโหลดหลายไฟล์พร้อมกัน**
- [ ] **Integration Test** — ทดสอบ API ทั้ง flow ตั้งแต่ Upload จนถึง List
- [ ] **Security** — เพิ่ม Security ที่เหมาะสมสำหรับ Backend
- [ ] **Deployment** — สามารถ Deploy ได้และใช้งานได้จริง

---

## เทคโนโลยีแนะนำ

| ส่วน    | ตัวอย่าง                                                                                         |
| ------- | ------------------------------------------------------------------------------------------------ |
| Backend | Node.js (Express/Fastify/NestJS), Go (Gin/Fiber), Python (FastAPI/Django), Bun.js (Hono/Elysia) |
| Storage | AWS S3, Google Cloud Storage, Cloudinary, MinIO (local), Azure Blob Storage, Supabase Storage, Cloudflare R2 |
| Database| PostgreSQL, MySQL, MongoDB, SQLite                                                               |
| Testing | Jest, Vitest, Mocha, pytest, Go testing                                                          |

---

## วิธีส่งงาน

1. สร้าง GitHub Repository ใหม่ของตัวเอง
2. พัฒนาตามข้อกำหนดด้านบน
3. **ต้องรัน test ผ่านทั้งหมดก่อนส่ง** (`npm test` หรือคำสั่งที่เทียบเท่า)
4. นำลิงก์ Repository ส่งกลับมาที่ <team@ddexpgroup.com>

## มีคำถาม?

หากมีข้อสงสัยเกี่ยวกับโจทย์ สามารถส่งอีเมลกลับมาสอบถามได้ที่ <team@ddexpgroup.com>
