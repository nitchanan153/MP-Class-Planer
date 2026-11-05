# MP Class Planner

เว็บแอปจัดตารางสอนของ Morpheus ทั้งแอปอยู่ในไฟล์เดียวคือ `index.html` (HTML + CSS + JS ไม่ต้อง build)

## มีอะไรบ้าง

| ไฟล์ | ใช้ทำอะไร |
|---|---|
| `index.html` | ตัวแอป: Board, Calendar, Billing, pay slip และใบเสร็จขนาด A5 |
| `vercel.json` | ตั้งค่า Vercel: ห้าม search engine เก็บหน้า และเพิ่ม security headers |
| `firestore.rules`, `firestore.indexes.json`, `firebase.json` | สำหรับย้ายไป Firebase ภายหลัง |

## ขึ้นเว็บด้วย Vercel

1. ที่ vercel.com เลือก Add New → Project แล้ว import repo นี้
2. Framework Preset เลือก **Other** ไม่ต้องตั้ง build command
3. กด Deploy ทุกครั้งที่ push ขึ้น `main` Vercel จะ deploy ให้เอง

## ความปลอดภัย

- ตารางสอนและชื่อนักเรียนใน `index.html` ถูกเข้ารหัส (AES-GCM) ต้องใส่รหัสผ่านของแต่ละบัญชีจึงจะเปิดได้
- **รหัสผ่านไม่ได้อยู่ใน repo นี้ และห้าม commit รหัสผ่านลงที่นี่**
- ห้าม commit ไฟล์ Excel ตารางสอน ไฟล์ export JSON หรือไฟล์ CSV และ PDF จากหน้า Billing เพราะในไฟล์เหล่านี้ชื่อนักเรียนไม่ได้เข้ารหัส (`.gitignore` กันไว้ให้แล้ว)
- แนะนำให้ตั้ง repo เป็น **Private**

## เปลี่ยนรหัสผ่าน

1. ล็อกอินด้วยบัญชีแอดมินบนเว็บจริง
2. ไปที่ Manage → Accounts → Sign-in passwords แล้วกด **Make new app file**
3. ระบบจะดาวน์โหลด `index.html` ไฟล์ใหม่มาให้ นำไปแทนไฟล์เดิมใน repo แล้ว commit และ push
4. หลัง Vercel deploy เสร็จ รหัสใหม่จึงจะใช้ได้
