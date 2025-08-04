---
icon: bolt-lightning
---

# Lightning network คืออะไร

### ความเป็นมาและการเกิดขึ้นของ Lightning Network <a href="#lightning-network" id="lightning-network"></a>

Lightning network เป็นนวัตกรรมสำคัญที่เกิดขึ้นเพื่อแก้ไขปัญหาการขยายขนาด (Scalability) ของบิตคอยน์ ซึ่งเป็นหนึ่งในความท้าทายหลักของเครือข่ายบิตคอยน์ ที่สามารถประมวลผลธุรกรรมได้เพียง 7 รายการต่อวินาทีเท่านั้น ทำให้เมื่อเปรียบเทียบกับระบบการชำระเงินแบบดั้งเดิมอย่าง VISA ที่สามารถประมวลผลธุรกรรมได้มากกว่า 6,000 รายการต่อวินาที โดยมีช้วงการพัฒนาดังนี้

#### การเกิดขึ้นของแนวคิด (2015-2016)

แนวคิดของ Lightning Network เริ่มต้นขึ้นในปี 2015 เมื่อนักพัฒนาสองคน คือ **Joseph Poon** และ **Thaddeus Dryja** ได้เผยแพร่ร่างแรกของ Lightning Network whitepaper [\[1\]](https://trustmachines.co/blog/bitcoin-in-2016-lightning-network-whitepaper/) งานวิจัยของพวกเขาอิงจากการอภิปรายก่อนหน้านี้เกี่ยวกับช่องทางการชำระเงิน (Payment Channels) โดย Satoshi Nakamoto ที่ได้อธิบายแนวคิดนี้ให้กับ Mike Hearn ซึ่งเป็นนักพัฒนาร่วม และได้เผยแพร่บทสนทนานี้ในปี 2013

ต่อมาในเดือนกุมภาพันธ์ 2015 Joseph Poon และ Thaddeus Dryja ได้เผยแพร่ Lightning Network whitepaper ฉบับแรก และในปี 2016 พวกเขาได้ก่อตั้งบริษัท **Lightning Labs** เพื่อทุ่มเทพัฒนา Lightning Network [\[2\]](https://ospreyfunds.io/newsletter/what-is-the-lightning-network/)

#### จุดเปลี่ยนสำคัญ: SegWit Activation (2017)

ความก้าวหน้าครั้งสำคัญเกิดขึ้นหลังจากการเปิดใช้งาน **Segregated Witness (SegWit)** ของบิตคอยน์ ในปี 2017 SegWit ได้เข้ามาแก้ไขปัญหา transaction malleability ซึ่งเป็นข้อผิดพลาดของบิตคอยน์ ที่มีมาอย่างยาวนาน และปัญหานี้เป็นอุปสรรคสำคัญต่อการพัฒนา Lightning Network

SegWit ถูกเปิดใช้งานอย่างเป็นทางการในวันที่ 24 สิงหาคม 2017 ที่บล็อกที่ 481,824 การอัปเดตนี้ไม่เพียงแต่เพิ่มความจุของบิตคอยน์  โดยการลดขนาดของธุรกรรม แต่ยังเปิดทางให้ Lightning Network สามารถทำงานได้อย่างปลอดภัย

#### การเปิดตัวบน Mainnet (2018)

เมื่อวันที่ 15 มีนาคม 2018 Lightning Labs ได้ประกาศเปิดตัว **lnd 0.4-beta** ซึ่งเป็นเวอร์ชันแรกที่สามารถใช้งานบน Bitcoin mainnet ได้ [\[3\]](https://lightning.engineering/posts/2018-03-15-lnd-beta/) ซึ่งนี่ถือเป็นจุดเปลี่ยนสำคัญที่ทำให้ Lightning Network เปลี่ยนจากการทดลองบน testnet มาสู่การใช้งานจริงด้วยบิตคอยน์จริง

และในเดือนเดียวกัน ทีมพัฒนาหลักทั้งสาม ได้แก่ **ACINQ**, **Blockstream**, และ **Lightning Labs** ได้ร่วมมือกันทำการทดสอบ interoperability และประสบความสำเร็จในการทำธุรกรรม Lightning แรก ๆ บน mainnet โดยใช้การใช้งานทั้งสามแบบ

### สถาปัตยกรรมเชิงเทคนิคของ Lightning Network <a href="#lightning-network" id="lightning-network"></a>

#### Payment Channels - ช่องทางการชำระเงิน

หัวใจหลักของ Lightning Network คือ **Payment Channels** ซึ่งเป็นช่องทางการชำระเงินระหว่างสองฝ่าย การสร้าง Payment Channel เริ่มต้นด้วยการสร้าง **Commitment Transaction** ที่ทั้งสองฝ่ายจะต้องโอนบิตคอยน์ จำนวนหนึ่งมาไว้ที่ address กลางที่ใช้ **Multisignature (Multisig)** ซึ่งจำเป็นต้องใช้ private key จากทั้งสองฝ่ายในการเอาเงินออก

การทำงานของ Payment Channel มีลักษณะดังนี้:

* การโอนบิตคอยน์มาที่ address กลางจะถูกบันทึกลง blockchain
* หลังจาก Commitment Transaction ถูกยืนยัน Payment Channel จะเกิดขึ้น
* ผู้ใช้สามารถทำธุรกรรมภายใน channel ได้ไม่จำกัดครั้งโดยไม่ต้องบันทึกลง blockchain
* เมื่อปิด channel ผลลัพธ์สุดท้ายจึงจะถูกบันทึกลง blockchain

#### Multisignature Technology

Lightning Network ใช้เทคโนโลยี **Multisignature** โดยเฉพาะแบบ **2-of-2 multisig** ซึ่งต้องการลายเซ็นจากทั้งสองฝ่ายในการปลดล็อกบิตคอยน์ เทคโนโลยีนี้ช่วยรับประกันความปลอดภัยและป้องกันการโกงในระบบ Payment Channel [\[4\]](https://bitcoinmagazine.com/technical/understanding-the-lightning-network-part-building-a-bidirectional-payment-channel-1464710791)

#### Hash Time-Locked Contracts (HTLCs)

**HTLC** เป็นสัญญาอัจฉริยะพิเศษที่ใช้ในการรับประกันความปลอดภัยของการชำระเงิน HTLCs ทำงานโดยใช้:

* **Hash locks**: ใช้ hash cryptographic เพื่อล็อกการชำระเงิน
* **Time locks**: กำหนดระยะเวลาที่การชำระเงินจะต้องเสร็จสิ้น
* หากไม่มีการเปิดเผย preimage ภายในเวลาที่กำหนด เงินจะถูกส่งคืนให้ผู้ส่ง [\[5\]](https://github.com/lightningnetwork/lightning-onion)

#### Routing และ Network Topology

Lightning Network ใช้ระบบ **source routing** ที่ให้ผู้ส่งมีการควบคุมเส้นทางการชำระเงินเต็มรูปแบบ ผู้ส่งสามารถกำหนด:

* จำนวน hops ทั้งหมดในเส้นทาง
* ค่าธรรมเนียมรวมที่จะจ่าย
* ระยะเวลา time-lock ที่ยอมรับได้

เครือข่ายไม่จำเป็นต้องสร้าง channel โดยตรงระหว่างผู้ใช้ทุกคน เช่น หาก Alice มี channel กับ Bob และ Bob มี channel กับ Charlie แม้ว่า Alice จะไม่มี channel โดยตรงกับ Charlie แต่ Alice ยังสามารถส่งเงินให้ Charlie ผ่าน Bob ได้

#### Onion Routing

Lightning Network ใช้ **Onion Routing** ที่ดัดแปลงมาจากโปรโตคอล **Sphinx** เพื่อปกป้องความเป็นส่วนตัวของผู้ใช้ ลักษณะการทำงาน:

* ข้อมูลการชำระเงินถูกเข้ารหัสเป็นชั้น ๆ เหมือนหัวหอม
* แต่ละ node สามารถถอดรหัสได้เพียงชั้นเดียวเท่านั้น
* Node แต่ละตัวรู้เพียงแค่ node ก่อนหน้าและถัดไปเท่านั้น
* ไม่มีใครรู้จุดหมายปลายทางหรือความยาวของเส้นทางนอกจากผู้ส่ง

การใช้ Onion Routing ช่วยให้ Lightning Network มีคุณสมบัติด้านความเป็นส่วนตัว:

* ผู้เข้าร่วมในเส้นทางไม่รู้ตำแหน่งที่แน่นอนในเส้นทาง
* ไม่รู้แหล่งที่มาและจุดหมายปลายทางของการชำระเงิน
* ไม่ทราบจำนวนผู้เข้าร่วมทั้งหมดในเส้นทาง
* แต่ละเส้นทางการชำระเงินแยกแยะไม่ได้จากกันทางคำนวณ

#### มาตรฐาน BOLT (Basis of Lightning Technology) <a href="#bolt-basis-of-lightning-technology" id="bolt-basis-of-lightning-technology"></a>

**BOLT** เป็นชุดมาตรฐานทางเทคนิคที่กำหนดกฎเกณฑ์และวิธีการสำหรับการทำงานของ Lightning Network BOLT ช่วยให้การใช้งานต่าง ๆ บน Lightning Network เพื่อให้สามารถสื่อสารและทำงานร่วมกันได้อย่างไร้รอยต่อ

ปัจจุบันมี BOLT ทั้งหมด 12 ฉบับ (BOLT 1 - 12) ที่ครอบคลุมหัวข้อต่าง ๆ :

* **BOLT #1**: Base Protocol - โปรโตคอลพื้นฐาน
* **BOLT #2**: Peer Protocol for Channel Management - การจัดการ channel
* **BOLT #3**: Bitcoin Transaction and Script Formats - รูปแบบธุรกรรมและ script
* **BOLT #4**: Onion Routing Protocol - โปรโตคอล onion routing
* **BOLT #5**: Recommendations for On-chain Transaction Handling - คำแนะนำการจัดการธุรกรรม on-chain
* **BOLT #7**: P2P Node and Channel Discovery - การค้นหา node และ channel
* **BOLT #8**: Encrypted and Authenticated Transport - การขนส่งที่เข้ารหัสและยืนยันตัวตน
* **BOLT #9**: Assigned Feature Flags - การกำหนด feature flags
* **BOLT #10**: DNS Bootstrap and Assisted Node Location - การช่วยค้นหาตำแหน่ง node
* **BOLT #11**: Invoice Protocol for Lightning Payments - โปรโตคอลใบแจ้งหนี้สำหรับการชำระเงิน Lightning
* BOLT #12: Negotiation Protocol for Lightning Payments

### ทีมพัฒนาหลักและการใช้งาน <a href="#undefined" id="undefined"></a>

#### ทีมพัฒนาหลัก

ปัจจุบันมีทีมหลักสามทีมที่ทำงานเกี่ยวกับการพัฒนา Lightning Network:

1. **Lightning Labs** - พัฒนา lnd (Lightning Network Daemon) โดยใช้ภาษา Go
2. **Blockstream** - พัฒนา c-lightning (Core Lightning) โดยใช้ภาษา C
3. **ACINQ** - พัฒนา Eclair โดยใช้ภาษา Scala

#### การพัฒนาบนมือถือ

ในปี 2018 ACINQ ได้เปิดตัว **Eclair Wallet** บนระบบปฏิบัติการ Android ซึ่งเป็นแอปพลิเคชันกระเป๋า Bitcoin บนมือถือตัวแรกที่รองรับ Lightning Network แอปนี้มีจุดเด่นคือ:

* รองรับทั้งธุรกรรม on-chain และ off-chain ในแอปเดียว
* การทำธุรกรรมที่รวดเร็วกว่า NFC
* ผู้ใช้ไม่จำเป็นต้องแยกกระเป๋าสำหรับการใช้งานที่แตกต่างกัน

### สถิติและการเติบโตของเครือข่าย <a href="#undefined" id="undefined"></a>

#### ความจุและขนาดของเครือข่าย

ตามข้อมูลล่าสุด (มกราคม 2025) Lightning Network มีสถิติดังนี้:

* **ความจุรวม**: 4,101.76 BTC (ประมาณ 437 ล้านดอลลาร์สหรัฐ)
* **จำนวน nodes สาธารณะ**: 11,306 nodes
* **จำนวน channels สาธารณะ**: 42,094 channels
* **ความจุเฉลี่ยต่อ node**: 0.362 BTC (ประมาณ 38,299 ดอลลาร์)
* **ความจุเฉลี่ยต่อ channel**: 0.097 BTC (ประมาณ 10,277 ดอลลาร์)

#### การเติบโตอย่างต่อเนื่อง

การวิเคราะห์จาก Fidelity Digital Assets แสดงให้เห็นว่า:

* ความจุเฉลี่ยของ channels เพิ่มขึ้น 214% ในช่วง 4 ปีที่ผ่านมา
* ความจุรวมเมื่อคิดเป็น Bitcoin เพิ่มขึ้น 384% ตั้งแต่ปี 2020
* เกิดการเปลี่ยนแปลงจาก channels หลายๆ ตัวที่มีขนาดเล็ก มาเป็น channels น้อยตัวแต่มีขนาดใหญ่และมีประสิทธิภาพมากขึ้น

จากข้อมูลของ CoinGate พบว่าสัดส่วนการชำระเงิน Bitcoin ผ่าน Lightning Network เพิ่มขึ้นอย่างต่อเนื่อง:

* ปี 2022: 5.98%
* ปี 2023: 7.95%
* ปี 2024: 14.51%

### Nodes ที่มีความจุสูงสุด

Nodes ที่มีความจุสูงสุดในเครือข่าย ได้แก่:

1. **Binance**: 553.99 BTC (13.51% ของเครือข่าย)
2. **OKX**: 465.90 BTC (11.36% ของเครือข่าย)
3. **ACINQ**: 420.61 BTC (10.25% ของเครือข่าย)
4. **Bitfinex nodes**: รวมกว่า 619 BTC [\[6\]](https://1ml.com/node?order=capacity)

### การพัฒนาล่าสุดและอนาคต <a href="#undefined" id="undefined"></a>

#### Taproot Assets และ Stablecoins

ในปี 2024 Lightning Labs ได้เปิดตัว **Taproot Assets v0.4** บน mainnet ซึ่งเปิดให้สามารถสร้างและโอนสินทรัพย์ดิจิทัลอย่าง stablecoins บน Bitcoin ผ่าน Lightning Network และในปี 2025 ได้อัปเดตเป็น **Taproot Assets v0.6** ที่ปรับปรุงการรองรับ stablecoins ให้ดีขึ้น

คุณสมบัติสำคัญของ Taproot Assets:

* ใช้ประโยชน์จากเครือข่าย Lightning ที่มีอยู่แล้ว
* ผู้ออกสินทรัพย์ไม่จำเป็นต้องสร้างเครือข่ายแยกต่างหาก
* รองรับการทำธุรกรรมทันทีและค่าธรรมเนียมต่ำ

#### ความร่วมมือกับ AI และเทคโนโลยีใหม่

Lightning Labs ได้พัฒนาเครื่องมือที่ช่วยให้นักพัฒนาสามารถผสานรวม Bitcoin และ Lightning เข้ากับ AI และ Large Language Models อย่าง ChatGPT โดยใช้โปรโตคอล **L402** และไลบรารี **Langchain**

#### การยอมรับจากธุรกิจขนาดใหญ่

ในปี 2024-2025 เริ่มมีธุรกิจขนาดใหญ่หันมาใช้ Lightning Network มากขึ้น:

* **Revolut** นำ Lightning มาใช้ในแพลตฟอร์มใน UK และ European Economic Area
* **Kraken** และ **Coinbase** รองรับการชำระเงิน Lightning
* การใช้งานใน fintech apps, การชำระเงินค้าปลีก, และ IoT payments เพิ่มขึ้น

### ข้อจำกัดและความท้าทาย <a href="#undefined" id="undefined"></a>

#### ปัญหาการกระจุกตัว

การขาดสภาพคล่องและความจำเป็นที่ต้องออนไลน์ตลอดเวลา ทำให้เกิดการกระจุกตัวของ nodes ขนาดใหญ่:

* Nodes ใหญ่ 10 อันดับแรก (0.4% ของเครือข่าย) ประมวลผล 53% ของธุรกรรม
* Nodes กว่า 2,500 ตัวที่เหลือประมวลผลเพียง 47%

#### ข้อจำกัดในการสำรองข้อมูล

กระเป๋า Lightning ไม่สามารถสำรองข้อมูลได้เหมือนกระเป๋าบิตคอยน์ ผู้ใช้จะสูญเสียเงินทั้งหมดในกรณีที่:

* ลืมรหัสผ่าน
* ไม่สามารถเข้าถึงอุปกรณ์ได้
* ข้อมูล channel สูญหาย

### บทสรุป <a href="#undefined" id="undefined"></a>

Lightning Network แสดงให้เห็นถึงการพัฒนาเทคโนโลジีที่สำคัญในระบบ Bitcoin โดยมีการเติบโตอย่างต่อเนื่องตั้งแต่การเปิดตัวในปี 2018 จากแนวคิดเดิมของ Joseph Poon และ Thaddeus Dryja ในปี 2015 ไปสู่การเป็นโซลูชันสำคัญสำหรับปัญหาการขยายขนาดของ Bitcoin

เทคโนโลยี Lightning Network ใช้หลักการของ Payment Channels, Multisignature, HTLCs, และ Onion Routing ในการสร้างเครือข่ายการชำระเงินที่รวดเร็ว ปลอดภัย และมีความเป็นส่วนตัว แม้จะยังมีข้อจำกัดในเรื่องการ routing และการกระจุกตัว แต่การพัฒนาอย่างต่อเนื่องและการยอมรับจากธุรกิจขนาดใหญ่ในปี 2024-2025 แสดงให้เห็นถึงศักยภาพในการเป็นโครงสร้างพื้นฐานสำคัญสำหรับระบบการเงินดิจิทัลในอนาคต

การพัฒนาล่าสุดอย่าง Taproot Assets และการผสานรวมกับเทคโนโลยี AI เปิดโอกาสให้ Lightning Network ไม่เพียงแต่เป็นเครื่องมือสำหรับการชำระเงิน Bitcoin เท่านั้น แต่ยังเป็นแพลตฟอร์มสำหรับสินทรัพย์ดิจิทัลหลากหลายประเภทและการใช้งานที่ซับซ้อนมากขึ้น

### อ้างอิง

\[1] [https://trustmachines.co/blog/bitcoin-in-2016-lightning-network-whitepaper/](https://trustmachines.co/blog/bitcoin-in-2016-lightning-network-whitepaper/)

\[2] [https://ospreyfunds.io/newsletter/what-is-the-lightning-network/](https://ospreyfunds.io/newsletter/what-is-the-lightning-network/)

\[3] [https://lightning.engineering/posts/2018-03-15-lnd-beta/](https://lightning.engineering/posts/2018-03-15-lnd-beta/)

\[4] [https://bitcoinmagazine.com/technical/understanding-the-lightning-network-part-building-a-bidirectional-payment-channel-1464710791](https://bitcoinmagazine.com/technical/understanding-the-lightning-network-part-building-a-bidirectional-payment-channel-1464710791)

\[5] [https://github.com/lightningnetwork/lightning-onion](https://github.com/lightningnetwork/lightning-onion)

\[6] [https://1ml.com/node?order=capacity](https://1ml.com/node?order=capacity)
