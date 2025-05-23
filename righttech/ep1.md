---
description: 'Right Tech EP.1 : B T C นั้นคืออะไร ยังไม่เข้าใจมาจนถึงวันนี้'
---

# Right Tech EP.1

{% embed url="https://www.youtube.com/watch?v=vvAcWpGaYZA" %}

## บิตคอยน์ (Bitcoin) ระบบเงินสดอิเล็กทรอนิกส์

ในยุคที่การทำธุรกรรมทางการเงินเกิดขึ้นผ่านอินเทอร์เน็ตเป็นหลัก “บิตคอยน์” (Bitcoin) ได้กลายเป็นปรากฏการณ์ครั้งใหญ่ที่พูดถึงการสร้างระบบเงินสดอิเล็กทรอนิกส์แบบกระจายศูนย์ (decentralized) โดยไม่มีธนาคารกลางหรือหน่วยงานกลางใดเป็นผู้ควบคุม แค่มีซอฟต์แวร์และ private key ก็สามารถส่งและรับมูลค่าได้เหมือนกับการโอนเงินทั่วไป แต่มีความโปร่งใส ปลอดภัย และไร้พรมแดน

***

### 1. โครงสร้างพื้นฐานของบิตคอยน์

#### 1.1 โปรโตคอลและซอฟต์แวร์โอเพนซอร์ส

* **โปรโตคอลบิตคอยน์** คือชุดกฎ (rules) ที่กำหนดวิธีการสร้าง บันทึก และตรวจสอบธุรกรรมทั้งหมดในเครือข่าย เช่น ขนาดบล็อก, ฟอร์แมตรายการธุรกรรม, วิธีคำนวณแฮช
* **ซอฟต์แวร์บิตคอยน์คอร์ (Bitcoin Core)** พัฒนาโดยชุมชนนักพัฒนาโอเพนซอร์ส หลักการทำงานทั้งระบบ (full node) รันซอฟต์แวร์นี้เพื่อตรวจสอบและเก็บข้อมูลบล็อกเชน
* ผู้ใช้ทั่วไปไม่จำเป็นต้องรันโหนดเต็มรูปแบบ แต่สามารถใช้ไลบรารีหรือ API ของผู้ให้บริการกระเป๋าสตางค์ (wallet service) เพื่อส่ง/รับบิตคอยน์

#### 1.2 เครือข่ายแบบ Peer‑to‑Peer

* เครือข่ายของบิตคอยน์เป็นแบบ **P2P (Peer‑to‑Peer)** ทุกโหนดเชื่อมต่อกันโดยตรง ไม่มีเซิร์ฟเวอร์ศูนย์กลาง
* เมื่อโหนดใดได้รับธุรกรรมใหม่ หรือบล็อกใหม่ มันจะ **gossip** กระจายไปยังโหนดอื่น ๆ ทันที ทำให้ข้อมูลแพร่กระจายทั่วโลกภายในไม่กี่วินาที

***

### 2. กระเป๋าเงินบิตคอยน์ (Wallet)

กระเป๋าเงินบิตคอยน์ทำหน้าที่จัดการ **กุญแจสาธารณะ (Public Key)** และ **กุญแจลับส่วนตัว (Private Key)** ซึ่งเปรียบเหมือนบัญชีธนาคารและลายเซ็นดิจิทัล

#### 2.1 ประเภทของ Wallet

| ประเภท          | การติดตั้ง               | การเชื่อมต่ออินเทอร์เน็ต | ความสะดวกใช้งาน | ความปลอดภัย   |
| --------------- | ------------------------ | ------------------------ | --------------- | ------------- |
| Desktop Wallet  | โปรแกรมบน PC/macOS/Linux | ตามการตั้งค่า (hot/cold) | ปานกลาง         | ปานกลาง – สูง |
| Mobile Wallet   | แอปบน iOS/Android        | ติด (hot wallet)         | สูง             | ต่ำ – ปานกลาง |
| Web Wallet      | ผ่านเว็บเบราว์เซอร์      | ติด (hot wallet)         | สูง             | ต่ำ           |
| Hardware Wallet | อุปกรณ์เชื่อม USB/NFC/QR | ไม่ติด (cold wallet)     | ต่ำ – ปานกลาง   | สูงมาก        |

* **Desktop Wallet**\
  ติดตั้งบนเครื่องคอมพิวเตอร์ ควบคุมกุญแจได้เต็มที่ หากตัวเครื่องออฟไลน์ (cold storage) จะปลอดภัยมากขึ้น แต่หากเปิดเชื่อมเน็ตอาจเสี่ยง
* **Mobile Wallet**\
  พกพาสะดวก ใช้งานง่าย เหมาะสำหรับการชำระเงินในชีวิตประจำวัน แต่เก็บกุญแจบนมือถือที่เชื่อมอินเทอร์เน็ตตลอดเวลา
* **Web Wallet**\
  ไม่ต้องติดตั้ง ใช้งานผ่านบราวเซอร์ แต่กุญแจมักเก็บบนเซิร์ฟเวอร์บุคคลที่สาม
* **Hardware Wallet**\
  เก็บกุญแจในอุปกรณ์เฉพาะ เซ็นธุรกรรมในอุปกรณ์โดยตรง ปลอดภัยสูงสุด มักนิยมใช้เก็บเหรียญปริมาณมาก

***

### 3. โครงสร้างและกลไกธุรกรรม

#### 3.1 โครงสร้างธุรกรรม

1. **อินพุต (Inputs)**
   * อ้างอิงธุรกรรมก่อนหน้า ที่ผู้ส่งเป็นเจ้าของ
   * ยืนยันด้วยลายเซ็นดิจิทัล (ECDSA / Schnorr)
2. **เอาต์พุต (Outputs)**
   * ระบุที่อยู่ (Bitcoin address) และจำนวนเหรียญที่จะส่ง
   * แต่ละเอาต์พุตกลายเป็นอินพุตในธุรกรรมถัดไปได้
3. **ค่าธรรมเนียม (Transaction Fee)**
   * ส่วนต่างระหว่างผลรวมอินพุตกับผลรวมเอาต์พุต
   * จ่ายให้ “นักขุด” เป็นแรงจูงใจ

#### 3.2 การตรวจสอบความถูกต้อง

* โหนดเต็มรูปแบบ (full node) ตรวจสอบ
  * รูปแบบธุรกรรมและสคริปต์ (script) ถูกต้อง
  * ลายเซ็นตรงกับคีย์สาธารณะ
  * ไม่มีการใช้จ่ายซ้ำ (double‑spend)

***

### 4. การขุดบิตคอยน์ (Mining)

#### 4.1 หลักการ Proof‑of‑Work

* นักขุดสร้าง **บล็อกต้นแบบ (candidate block)** ประกอบด้วย
  * ธุรกรรมที่รอการยืนยัน
  * รายละเอียดบล็อกก่อนหน้า (previous block hash)
  * ค่า nonce แบบสุ่ม
* แฮชของบล็อกเฮดเดอร์ต้องต่ำกว่าเป้าหมาย (target threshold) ตามที่โปรโตคอลกำหนด
  * ยิ่งเป้าหมายต่ำ ต้องใช้การลองสุ่ม (trial‑and‑error) มากขึ้น

#### 4.2 รางวัลและแรงจูงใจ

* **รางวัลบล็อก (Block Reward)**\
  ปัจจุบันอยู่ที่ 6.25 BTC ต่อบล็อก (จะ Halving ทุก 210,000 บล็อก ≈ 4 ปี)
* **ค่าธรรมเนียมรวม (Total Fees)**\
  ค่าธรรมเนียมจากธุรกรรมในบล็อก

#### 4.3 บทบาทด้านความปลอดภัย

* การใช้พลังงานไฟฟ้าจำนวนมากทำให้การปลอมแปลง (rewriting history) ต้องมีต้นทุนสูง นักขุดจึงไม่พยายามทำ

***

### 5. บล็อกเชน (Blockchain)

#### 5.1 โครงสร้างการเชื่อมต่อ

* บล็อกเชนคือ “ห่วงโซ่” ของบล็อก
* ทุกบล็อกมีรายการ:
  * **Merkle Root** สรุปลายเซ็นธุรกรรมทั้งหมด
  * **Previous Block Hash** อ้างอิงไปยังบล็อกก่อนหน้า
  * **Timestamp**, **Difficulty**, **Nonce**

#### 5.2 คุณสมบัติสำคัญ

* **ไม่เปลี่ยนแปลงย้อนหลัง (Immutable)**\
  เมื่อบล็อกถูกยืนยัน จะเก็บเป็นหลักฐานถาวร
* **โปร่งใส (Transparent)**\
  ทุกคนตรวจสอบประวัติธุรกรรมได้บนเครือข่ายสาธารณะ
* **กระจายศูนย์ (Decentralized)**\
  ไม่มีจุดควบคุมเดียว จึงทนทานต่อการโจมตี

***

### 6. โหนดบิตคอยน์ (Bitcoin Node)

#### 6.1 ประเภทโหนด

* **Full Node**\
  เก็บบล็อกเชนทั้งหมด ตรวจสอบกฎโปรโตคอล
* **Lightweight SPV Node**\
  เก็บเพียงส่วนหัวบล็อก (block headers) และธุรกรรมที่เกี่ยวข้อง

#### 6.2 หน้าที่หลักของ Full Node

1. **เก็บสำรองบล็อกเชนทั้งหมด**
2. **ตรวจสอบธุรกรรมและบล็อกใหม่** ก่อนเก็บ
3. **กระจายข้อมูล** ให้โหนดอื่น ๆ

การรัน Full Node ช่วยรักษาความเป็นอิสระและความปลอดภัยของเครือข่าย

***

### 7. การประยุกต์ใช้งานและข้อควรระวัง

#### 7.1 การนำไปใช้งาน

* **ชำระค่าสินค้า/บริการ**
* **แหล่งเก็บมูลค่า (Store of Value)** เช่น ทองคำดิจิทัล
* **ระบบการเงินไร้พรมแดน** ไม่ต้องพึ่งธนาคารกลาง

#### 7.2 ข้อจำกัดและความเสี่ยง

* **ความผันผวนของราคา** สูงกว่าสกุลเงินทั่วไป
* **ค่าธรรมเนียมธุรกรรม** แปรผันตามความแออัดของเครือข่าย
* **ประเด็นด้านสิ่งแวดล้อม** การขุดใช้พลังงานสูง
* **การกำกับดูแล** หลายประเทศออกกฎหมายควบคุมต่างกัน

***

### 8. สรุป

บิตคอยน์นำเสนอรูปแบบใหม่ของเงินสดอิเล็กทรอนิกส์ที่ไม่ต้องพึ่งธนาคารหรือหน่วยงานกลาง ทุกธุรกรรมถูกบันทึกบนบล็อกเชนที่กระจายอยู่ทั่วโลก ผ่านระบบ Proof‑of‑Work ที่นักขุดเป็นผู้ยืนยันความถูกต้อง ทำให้เกิดเครือข่ายที่ **โปร่งใส ปลอดภัย และไร้พรมแดน** ผู้ใช้เข้าถึงผ่านกระเป๋าเงินหลายรูปแบบตามระดับความต้องการ ตั้งแต่เดสก์ท็อป โมบาย จนถึงฮาร์ดแวร์เฉพาะทาง แม้จะมีความท้าทายเรื่องความผันผวนและการใช้พลังงาน แต่บิตคอยน์ก็ยังคงเป็นจุดเริ่มต้นของโลกการเงินดิจิทัลในยุคอนาคต
