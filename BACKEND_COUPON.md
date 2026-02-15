# การบ้าน Backend: ระบบ Apply Coupon

## โจทย์

สร้างฟังก์ชัน `applyCoupon` สำหรับระบบใช้คูปองส่วนลดในระบบซื้อขายสินค้า โดยรับข้อมูลตะกร้าสินค้าและรหัสคูปอง แล้วคำนวณส่วนลดและยอดรวมสุทธิกลับมา

---

## สิ่งที่ต้องทำ

- [ ] สร้างฟังก์ชัน `applyCoupon` ที่รับ Input (couponCode, cartTotal, shippingFee) แล้ว return ผลการคำนวณ
- [ ] รองรับคูปองทั้ง 3 ประเภท: `fixed_amount`, `percentage`, `free_shipping`
- [ ] คำนวณส่วนลดให้ถูกต้อง รวมถึงกรณีทศนิยมและเพดานส่วนลด (maxDiscount)
- [ ] ตรวจสอบเงื่อนไขการใช้คูปอง: หมดอายุ, ยังไม่ถึงวันเริ่มใช้, ยอดขั้นต่ำ, จำนวนที่เหลือ
- [ ] Return Error Code ที่ถูกต้องตามแต่ละกรณี
- [ ] เขียน Unit Test (ไม่บังคับ)

---

## ข้อมูลตั้งต้น

ผู้สมัครสามารถใช้ข้อมูล Mock หรือ Seed Data ด้านล่างนี้เพื่อทดสอบ โดยไม่จำเป็นต้องสร้าง API สำหรับจัดการข้อมูลเหล่านี้

### Coupons

ให้ใช้ Mock Data ชุดนี้เป็นข้อมูลคูปองในระบบ:

```json
[
  {
    "code": "FIXED100",
    "type": "fixed_amount",
    "value": 100,
    "minPurchase": 500,
    "maxDiscount": null,
    "usedCount": 10,
    "remaining": 90,
    "startDate": "2025-01-01T00:00:00Z",
    "expiryDate": "2026-12-31T23:59:59Z"
  },
  {
    "code": "PERCENT15",
    "type": "percentage",
    "value": 15,
    "minPurchase": 300,
    "maxDiscount": 500,
    "usedCount": 49,
    "remaining": 1,
    "startDate": "2025-01-01T00:00:00Z",
    "expiryDate": "2026-12-31T23:59:59Z"
  },
  {
    "code": "FREESHIP",
    "type": "free_shipping",
    "value": 0,
    "minPurchase": 200,
    "maxDiscount": null,
    "usedCount": 5,
    "remaining": 195,
    "startDate": "2025-01-01T00:00:00Z",
    "expiryDate": "2026-12-31T23:59:59Z"
  },
  {
    "code": "EXPIRED50",
    "type": "fixed_amount",
    "value": 50,
    "minPurchase": 0,
    "maxDiscount": null,
    "usedCount": 10,
    "remaining": 90,
    "startDate": "2024-01-01T00:00:00Z",
    "expiryDate": "2024-12-31T23:59:59Z"
  },
  {
    "code": "FIXED50",
    "type": "fixed_amount",
    "value": 50,
    "minPurchase": 0,
    "maxDiscount": 1000,
    "usedCount": 10,
    "remaining": 0,
    "startDate": "2025-01-01T00:00:00Z",
    "expiryDate": "2026-12-31T23:59:59Z"
  },
  {
    "code": "FIXED99",
    "type": "fixed_amount",
    "value": 99.99,
    "minPurchase": 500,
    "maxDiscount": null,
    "usedCount": 20,
    "remaining": 30,
    "startDate": "2025-01-01T00:00:00Z",
    "expiryDate": "2026-12-31T23:59:59Z"
  },
  {
    "code": "PERCENT7H",
    "type": "percentage",
    "value": 7.5,
    "minPurchase": 200,
    "maxDiscount": 150.50,
    "usedCount": 30,
    "remaining": 70,
    "startDate": "2025-01-01T00:00:00Z",
    "expiryDate": "2026-12-31T23:59:59Z"
  },
  {
    "code": "FUTURE50",
    "type": "fixed_amount",
    "value": 50,
    "minPurchase": 0,
    "maxDiscount": null,
    "usedCount": 0,
    "remaining": 100,
    "startDate": "2099-01-01T00:00:00Z",
    "expiryDate": "2099-12-31T23:59:59Z"
  }
]
```

| code      | จุดประสงค์ในการทดสอบ                                      |
| --------- | -------------------------------------------------------- |
| FIXED100  | คูปองลดจำนวนเงินคงที่ ใช้งานได้ปกติ                       |
| PERCENT15 | คูปองลดเปอร์เซ็นต์ มี maxDiscount, usedCount ใกล้ limit   |
| FREESHIP  | คูปองส่งฟรี ใช้งานได้ปกติ                                  |
| EXPIRED50 | คูปองหมดอายุแล้ว (expiryDate ผ่านไปแล้ว)                   |
| FIXED50   | คูปองถูกใช้ครบจำนวนแล้ว (remaining = 0)                    |
| FIXED99   | คูปองลดจำนวนเงินที่มีค่าเป็นทศนิยม (99.99)                |
| PERCENT7H | คูปองลดเปอร์เซ็นต์ทศนิยม (7.5%) และ maxDiscount ทศนิยม (150.50) |
| FUTURE50  | คูปองยังไม่ถึงวันเริ่มใช้งาน (startDate อยู่ในอนาคต)       |

### ประเภทคูปอง

| type          | คำอธิบาย                                                        |
| ------------- | --------------------------------------------------------------- |
| fixed_amount  | ลดเป็นจำนวนเงินคงที่ เช่น ลด 100 บาท                           |
| percentage    | ลดเป็นเปอร์เซ็นต์ของยอดสินค้า มี maxDiscount เป็นเพดานส่วนลด   |
| free_shipping | ลดค่าจัดส่งทั้งหมด (ส่วนลดจะเท่ากับค่า shippingFee ที่ส่งมา)   |

---

## ฟังก์ชันที่ต้องสร้าง

### `applyCoupon(input)`

ฟังก์ชันสำหรับใช้คูปองกับตะกร้าสินค้า

**Input:**

```typescript
{
  couponCode: string;    // รหัสคูปอง
  cartTotal: number;     // ยอดรวมสินค้าในตะกร้า (ก่อนลด)
  shippingFee: number;   // ค่าจัดส่ง
}
```

**Output (สำเร็จ):**

```typescript
{
  success: true,
  data: {
    couponCode: "PERCENT15",
    type: "percentage",
    discount: 180.00,
    shippingDiscount: 0,
    cartTotal: 1200.00,
    shippingFee: 50.00,
    grandTotal: 1070.00
  }
}
```

**Output (ผิดพลาด):**

```typescript
{
  success: false,
  error: {
    code: "COUPON_EXPIRED",
    message: "This coupon has expired"
  }
}
```

**การคำนวณ grandTotal:**

```
grandTotal = cartTotal - discount + shippingFee - shippingDiscount
```

**ตัวอย่างการคำนวณแต่ละประเภท (shippingFee=50):**

| ประเภท | ตัวอย่าง |
| --- | --- |
| fixed_amount | cartTotal=1200, coupon=FIXED100 → discount=100, grandTotal = 1200 - 100 + 50 - 0 = **1150** |
| percentage | cartTotal=1200, coupon=PERCENT15 → discount=180 (15% of 1200, ไม่เกิน maxDiscount 500), grandTotal = 1200 - 180 + 50 - 0 = **1070** |
| percentage (ติดเพดาน) | cartTotal=5000, coupon=PERCENT15 → 15% of 5000 = 750 แต่เกิน maxDiscount 500 → discount=500, grandTotal = 5000 - 500 + 50 - 0 = **4550** |
| percentage (ทศนิยม) | cartTotal=999, coupon=PERCENT7H → 7.5% of 999 = 74.925 → discount=74.93 (ปัดขึ้น 2 ตำแหน่ง), grandTotal = 999 - 74.93 + 50 - 0 = **974.07** |
| free_shipping | cartTotal=1200, coupon=FREESHIP → shippingDiscount=50, grandTotal = 1200 - 0 + 50 - 50 = **1200** |

> **หมายเหตุ:** ผลลัพธ์ที่เป็นทศนิยมให้ปัดเป็น 2 ตำแหน่ง

---

## Error Codes

ฟังก์ชันต้อง return error ในรูปแบบ `{ success: false, error: { code, message } }` ตาม code ด้านล่าง:

| Code                 | คำอธิบาย                                    |
| -------------------- | ------------------------------------------- |
| COUPON_NOT_FOUND     | ไม่พบคูปองที่ระบุ                            |
| COUPON_EXPIRED       | คูปองหมดอายุแล้ว                             |
| COUPON_USAGE_LIMIT   | คูปองถูกใช้ครบจำนวนแล้ว (remaining = 0)       |
| COUPON_NOT_YET_VALID | คูปองยังไม่ถึงวันเริ่มใช้งาน                  |
| MIN_PURCHASE_NOT_MET | ยอดสั่งซื้อไม่ถึงขั้นต่ำที่คูปองกำหนด         |
| INVALID_REQUEST      | ข้อมูล Input ไม่ถูกต้อง (เช่น ค่าติดลบ)      |

---

## Unit Test (ไม่บังคับ)

ตัวอย่าง test cases ขั้นต่ำที่ควรมี:

**applyCoupon**

**Success Cases**

- `should apply fixed_amount coupon and reduce cart total correctly`
- `should apply percentage coupon and cap discount at maxDiscount`
- `should apply free_shipping coupon and remove shipping fee`
- `should apply coupon with fractional values and round to 2 decimal places`

**Error Cases**

- `should return COUPON_NOT_FOUND for invalid coupon code`
- `should return COUPON_EXPIRED for expired coupon`
- `should return COUPON_NOT_YET_VALID when coupon start date is in the future`
- `should return COUPON_USAGE_LIMIT when coupon remaining is 0`
- `should return MIN_PURCHASE_NOT_MET when cart total is below minimum`

> หากมี test cases เพิ่มเติมนอกเหนือจากนี้ จะได้รับคะแนนพิเศษ

---

## คะแนนพิเศษ (ทำได้ยิ่งดี แต่ไม่บังคับ)

- [ ] **Validation** — ตรวจสอบ Input อย่างรัดกุม เช่น ค่าติดลบ, ค่าเป็น string แทน number
- [ ] **เพิ่ม Test Cases** — ครอบคลุม Edge Cases เพิ่มเติม
- [ ] **TypeScript** — เขียนด้วย TypeScript พร้อม Type Definition ที่ครบถ้วน
