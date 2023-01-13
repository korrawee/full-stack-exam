## # Fullstack Exam

## Exam
## 1. System Design

#### Demo projects 
- [Backend](https://github.com/korrawee/backend-short-url.git)
- [Fronend](https://github.com/korrawee/frontend-short-url.git)

### Requirements
1. เมื่อมีการส่ง request มาพร้อมกับ  URL ยาวเข้ามา ระบบจะต้องสร้าง URL สั้นและส่งข้อมูลกลับไป
2. เมื่อมีการส่ง request มาด้วย URL สั้น ระบบต้อง redirect ไปยัง URL ยาว
3. รูปแบบของ URL สั้น คือ http://shorturl.com/<URL code>
_ตัวอย่าง  ย่อ http://my-order.ai/long-url/very-sub-path ให้เป็น http://shorturl.com/abc_

### back-end
เลือกใช้ NestJS เนื่องจากใช้ 3-tiers architecture ทำให้สามารถ scale โปรเจคได้ง่าย และ รองรับ Typescript ซึ่งสามารถช่วยลดข้อผิดพลาดระหว่างการพัฒนาได้ โดย service ภายในมีดังนี้:
#####  สร้าง URL สั้น จาก URL ยาว
1. นำ URL ยาว มาทำการ hash และตัด 10 ตัวแรกมาทำ URL code
2. บันทึกข้อมูลไปยัง database ในรูปแบบ { urlCode, URL ยาว }
3. ส่งค่า URL สั้นไปยังผู้ใช้งานในรูปแบบ http://shorturl.com/<URL code>

#####  redirect ไปยัง URL ยาว
1.  แยก URL code ออกจาก URL สั้น
2.  นำ URL code ไปดึงข้อมูล URL ยาว จาก database
3.   redirect ผู้ใช้ไปยัง URL ยาว

### database

##### MongoDB

เพราะจาก requriement ด้านบน ข้อมูลของ URL เหมาะกับการเก็บในรูปแบบ  key-value pair  และ ข้อมูลไม่จำเป็นต้องมีความสัมพันธ์ (relation) กับข้อมูลอื่น ๆ ดั้งนั้น จึงเลือก MongoDB ที่เป็น NoSQL

### front-end
ReactJS เพราะการลักษณะการใช้งานเกี่ยวกับ URL มักเป็นงานใช้งานบน web browser จึงเลือกใช้ web framework และ ReactJS ที่มีการแบ่งส่วนต่าง ๆ ออกเป็น component ย่อยทำให้สามารถนำไป reuse ได้สะดวก
### deployment
ใช้ AWS EC2 เนื่องเป็น could platform ทำให้ scaling ได้สะดวก และ AWS ยังมี load balancer service ที่สามารถใช้ร่วมกับ EC2 instance ได้

## 2. Up To You
### วิธีในการแก้ไขปัญหา server ที่มีปริมาณ request สูง
1. ทำ Caching เพื่อลดเวลาการดึงข้อมูล สำหรับข้อมูลที่ถูกเรียกใช้บ่อย
2. ทำ Load balancing ในการกระจาย traffic ไปยัง server ต่าง ๆ 
3. ทำการ scale  ระบบ โดยอิงจากสาเหตุ เช่น หาก load  เพิ่มขึ้นจากการประมวลผล ควรทำการ horizontal scaling เป็นต้น ซึ่ง scale สามารถทำได้อัตโนมัติผ่านผู้ให้บริการ cloud อาทิ เช่น Google Cloud และ AWS
4. ใช้ message queuing เช่น Kafka ในการจัดการ traffic เพื่อจัดลำดับการประมวลผล และให้ worker process ทำการ  subscribe ข้อมูลไปประมวลผล และ ส่งกลับไปยัง server หลักเพื่อ response ไปยังผู้ใช้งานได้
