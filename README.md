# MP Class Planner

เว็บแอปจัดตารางสอนของ Morpheus
- ทุกคนล็อกอินด้วยอีเมลและรหัสผ่านของตัวเอง
- ข้อมูลเก็บที่ Firebase (Firestore) ทุกเครื่องเห็นข้อมูลชุดเดียวกันและอัปเดตทันที
- สิทธิ์ถูกบังคับที่เซิร์ฟเวอร์ด้วย `firestore.rules`

## ไฟล์

| ไฟล์ | ใช้ทำอะไร |
|---|---|
| `index.html` | ตัวแอป ได้แก่ Board, Calendar, Billing, pay slip และใบเสร็จ A5 รวมถึงการเชื่อม Firebase (โปรเจกต์ `mp-class-planner`) |
| `firebase-sdk.js` | Firebase JS SDK 13.2 (app, auth, firestore) รวมเป็นไฟล์เดียว (ลิขสิทธิ์ Apache-2.0 ดูท้ายไฟล์) |
| `firestore.rules` | กฎสิทธิ์ ใครอ่านหรือเขียนอะไรได้บ้าง ต้องนำไปวางใน Firebase console |
| `firestore.indexes.json` | index ที่ query ของครูต้องใช้ (`classes`: `tutorId` + `date`) |
| `vercel.json` | ตั้งค่า Vercel ไม่ให้ search engine เก็บหน้า และเพิ่ม security headers |

## ตั้งค่า Firebase (ทำครั้งเดียว)

1. **Authentication** → Sign-in method → เปิด **Email/Password**
2. **Firestore Database** → แท็บ **Rules** → ลบของเดิมแล้ววางเนื้อหาทั้งหมดของ `firestore.rules` → กด **Publish**
3. **Firestore Database** → แท็บ **Indexes** → Composite → **Create index** แล้วตั้งค่าดังนี้
   - Collection ID: `classes`
   - Fields: `tutorId` Ascending และ `date` Ascending
   - Query scope: Collection
4. เปิดเว็บ (Vercel) → กด **Create the admin account** → สร้างบัญชีแอดมิน
5. หน้าถัดไปเลือกแหล่งข้อมูลที่จะนำเข้า แล้วกด **Finish setup**
6. **Manage → Accounts → Add a sign-in account:** สร้างบัญชีให้ครูทีละคน แล้วส่งอีเมลกับรหัสผ่านให้ครู

ถ้ายังไม่ได้สร้าง index ตามข้อ 3 ครูก็ยังเห็นคลาสของตัวเองได้ แต่แอปจะต้องโหลดคลาสทั้งหมดของครูคนนั้นทุกครั้ง ทำให้อ่านข้อมูลมากกว่าที่ควร

## เมื่อ `firestore.rules` เปลี่ยน

ทุกครั้งที่ไฟล์ `firestore.rules` ในรีโปนี้มีการแก้ไข ให้ไปที่ **Firestore Database → Rules** ลบของเดิม วางเนื้อหาใหม่ทั้งหมด แล้วกด **Publish** (Vercel อัปเดตเฉพาะหน้าเว็บ ไม่ได้อัปเดตกฎใน Firebase)

- ครั้งล่าสุด: เพิ่ม `statusLog` ให้ครูบันทึกได้ว่าใครเปลี่ยนสถานะคลาส ถ้ายังไม่ได้ Publish ครูยังเปลี่ยนสถานะได้ตามปกติ แต่จะไม่มีชื่อครูในประวัติ
- ก่อนหน้านั้น: เพิ่ม `dayNotes` สำหรับ Note รายวันของครูแต่ละคนในหน้า Calendar → Month

## รหัสผ่าน

- **เปลี่ยนรหัสผ่าน:** กดที่ชื่อมุมขวาบน → **Change password**
- **ลืมรหัสผ่าน:** ที่หน้าล็อกอินพิมพ์อีเมลแล้วกด **Forgot your password?** ระบบจะส่งลิงก์ตั้งรหัสใหม่ไปที่อีเมล
- **แอดมินส่งลิงก์ให้คนอื่น:** Manage → Accounts → **Send reset link**
- **ลบหรือปิดบัญชี:** ทำได้ที่ Firebase console → Authentication → Users

## ความปลอดภัย

- ค่าใน `FIREBASE_CONFIG` ไม่ใช่ความลับ สิ่งที่ปกป้องข้อมูลคือ `firestore.rules` และการล็อกอิน
- ครูได้รับเฉพาะคลาสของตัวเอง ค่าสอน ข้อมูลนักเรียน และการจ่ายเงินจะไม่ถูกส่งไปที่เครื่องครู
- ห้าม commit ไฟล์ Excel, CSV, PDF หรือ JSON ที่ export มา เพราะในไฟล์เหล่านี้ชื่อนักเรียนไม่ได้เข้ารหัส
- แนะนำให้ตั้ง repo เป็น **Private**

## โควตาแพ็กเกจฟรี (Spark)

- อ่านได้ 50,000 ครั้งต่อวัน และเขียนได้ 20,000 ครั้งต่อวัน
- แอปโหลดคลาสทีละเดือน (เดือนก่อน เดือนนี้ เดือนหน้า) ส่วนเดือนอื่นจะโหลดเมื่อเปิดดู
- หน้า Billing จะโหลดทุกเดือนตั้งแต่เดือนแรก เพื่อใช้คำนวณเครดิต
- ถ้าใช้เกินโควต้า Firestore จะหยุดทำงานจนถึงวันถัดไป ถ้าใช้งานมากขึ้นเรื่อย ๆ ให้เปลี่ยนเป็นแพ็กเกจ Blaze ซึ่งคิดเงินตามที่ใช้จริง สำหรับโรงเรียนขนาดนี้ค่าใช้จ่ายน่าจะน้อยมาก
