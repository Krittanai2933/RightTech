---
description: >-
  สำหรับผู้คนที่สนใจเกี่ยวกับการพัฒนาเพื่อป้องกันอันตรายที่อาจมาจาก quantum
  computer
icon: shield
---

# BIP 360: Pay to Quantum Resistant Hash

{% embed url="https://youtu.be/BrYa8HuM-1o" %}
เนิื้อหาในคลิปมีการใช้ AI ในการสร้างเสียง และเนื้อหาไม่ครบถ้วนเท่าบทความด้านล่าง จัดทำเพื่อใครที่ต้องการทำความเข้าใจแบบพื้นฐานก่อนจะลงลึก
{% endembed %}

**BIP 360: Pay to Quantum Resistant Hash** เป็นเอกสารข้อเสนอ (BIP - Bitcoin Improvement Proposal) ที่อยู่ในสถานะร่าง ณ วันที่ 2024-12-18 โดยมีวัตถุประสงค์เพื่อ **แนะนำประเภทเอาต์พุตใหม่ที่ใช้ลายเซ็นซึ่งอิงจากการเข้ารหัส Post-Quantum Cryptography (PQC)** เพื่อเพิ่มความปลอดภัยให้กับ Bitcoin จากภัยคุกคามที่อาจเกิดขึ้นจากคอมพิวเตอร์ควอนตัม แนวทางนี้เสนอให้ดำเนินการผ่าน **soft fork** โดยไม่จำเป็นต้องใช้ hard fork หรือเพิ่มขนาดบล็อก

**ปัญหาที่อาจจะเกิดจาก quantum computer**

ภัยคุกคามหลักที่มาจาก **Cryptoanalytically-Relevant Quantum Computers (CRQCs)** ต่อบิตคอยน์คือศักยภาพในการทำลายการเข้ารหัส Elliptic Curve Cryptography (ECC) ซึ่งใช้ในการรักษาความปลอดภัยของลายเซ็นและ Taproot commitments ของบิตคอยน์อย่าง **Shor's algorithm** เองก็จะถูก CRQC แก้ Discrete Logarithm Problem (DLP) ได้เร็วกว่าวิธีแบบคลาสสิกมาก ซึ่งทำให้สามารถ **ถอดรหัส private key จาก public key** ได้ และการเพิ่มความยาว public key เป็นสองเท่า (เช่น secp512k1 สมมติฐาน) จะช่วยได้เพียงเล็กน้อยและไม่เพียงพอ

แหล่งข้อมูลหนึ่งประเมินว่าในปี 2020 ประมาณ 25 % ของบิตคอยน์ทั้งหมดนั้น อยู่ในแอดเดรสที่เสี่ยงต่อการโจมตีด้วยควอนตัม และ ณ เวลาที่เขียนนั้นตัวเลขนี้ใกล้เคียงกับ 20 % นอกจากนี้ เหล่านักพัฒนาบางรายยังประเมินว่าอาจมีแอดเดรสที่เสี่ยงมากกว่านี้ถึง 5 ถึง 10 ล้านบิตคอยน์เลยทีเดียว

โดยทั่วไป public key จะถูกเปิดเผยบนบล็อกเชนเมื่อมีการใช้จ่าย transaction ทำให้เสี่ยงต่อการโจมตีด้วยควอนตัมจนกว่าจะถูกขุด การเปิดเผย public key บนเชนนี้เป็นขั้นตอนสำคัญสำหรับความปลอดภัยควอนตัม เพราะหากไม่เช่นนั้น จะต้องมีการย้ายเงินไปยังแอดเดรสใหม่เป็นประจำเพื่อป้องกัน "long-exposure CRQC attack"

**P2QRH เสนอเพื่อลดความเสี่ยงเหล่านี้** โดยไม่ต้องการให้ public key ถูกเปิดเผยบนเชนเหมือนกับ output types ที่มีอยู่ ซึ่งรวมถึง P2PKH, P2SH, P2WPKH, และ P2WSH ที่ public key จะถูกเปิดเผยใน input script เมื่อใช้จ่าย Taproot (P2TR) ก็ถือว่าเสี่ยงเช่นกันเพราะเข้ารหัส x-only public key ขนาด 32 ไบต์ ซึ่งสามารถสร้าง full public key ขึ้นมาใหม่ได้ หาก CRQC ได้รับ extended public key (xpub) ก็สามารถถอดรหัส non-hardened child private keys ได้

**ประเภทของการโจมตีด้วยควอนตัม**:

* **Long Exposure Quantum Attack:** Public key ถูกเปิดเผยบนบล็อกเชนเป็นเวลานาน ทำให้ผู้โจมตีมีโอกาสเพียงพอที่จะทำลายการเข้ารหัส การโจมตีนี้ส่งผลกระทบต่อ:
  * P2PK outputs
  * แอดเดรสที่ใช้ซ้ำ (ทุกประเภท ยกเว้น P2QRH)
  * Taproot addresses (ขึ้นต้นด้วย bc1p)
  * Extended public keys (xpubs)
  * Wallet descriptors
* **Short Exposure Quantum Attack:** การโจมตีที่ต้องดำเนินการอย่างรวดเร็วในขณะที่ transaction ยังอยู่ใน mempool ก่อนที่จะถูกขุดเข้าสู่บล็อก การโจมตีนี้ส่งผลกระทบต่อ:
  * Transaction ใดๆ ใน mempool (ยกเว้น P2QRH)
  * การโจมตีแบบ short-exposure ต้องการ CRQC ที่ใหญ่กว่าและมีราคาแพงกว่ามาก แหล่งข้อมูลหนึ่งประมาณว่า CRQC ขนาด 28 ล้าน physical qubits จะใช้เวลา 8.3 วินาทีในการคำนวณ 256-bit key

BIP 360 เสนอการใช้ P2QRH เพื่อปกป้อง transaction ที่ส่งเข้า mempool และช่วยรักษาตลาดเสรีโดยป้องกันความจำเป็นในการทำ private, out-of-band mempool transactions

แม้ภัยคุกคามหลักจาก CRQCs คือ signature แต่ก็ยังมีภัยคุกคามที่เล็กกว่าต่อ hash algorithms ของบิตคอยน์เช่นกัน Grover's algorithm สามารถเพิ่มความเร็วแบบ quadratic speedup ในการโจมตีแบบ brute-force ต่อ hash functions ที่ใช้ในบิตคอยน์ได้ แต่ต้องใช้ CRQC ที่มีประสิทธิภาพสูงกว่าอย่างมาก และอย่างไรก็ตามผลกระทบเชิงปฏิบัติของการโจมตีควอนตัมต่อ SHA-256 นั้นยังคงเป็นเพียงทฤษฎีเท่านั้น

**ข้อเสนอเพื่อแก้ไขปัญหานี้: Pay to Quantum Resistant Hash (P2QRH)**

BIP 360 เป็น BIP แรกในซีรีส์ภายใต้ soft fork ที่เรียกว่า **QuBit**

* **ประเภทเอาต์พุตใหม่:** แนะนำ P2QRH ที่ใช้ PQC signature algorithm
* **SegWit เวอร์ชัน 3:** P2QRH ใช้ SegWit เวอร์ชัน 3 ทำให้แอดเดรสขึ้นต้นด้วย **bc1r** ซึ่งอ้างอิงตาม BIP-173
* **การเข้ารหัสแบบ Hybrid:** P2QRH มีจุดประสงค์เพื่อนำมาใช้บน Taproot (P2TR) โดยรวมความปลอดภัยของ Schnorr signatures แบบคลาสสิกเข้ากับการเข้ารหัส post-quantum นี่เป็นรูปแบบของ hybrid cryptography ที่ช่วยให้แน่ใจว่าจะไม่มีการลดระดับความปลอดภัยหากพบช่องโหว่ใน signature algorithm ใดอัลกอริทึมหนึ่งที่ใช้
* **Hash Commitment:** P2QRH จะเข้ารหัส **hash ของ public key** แทนที่จะเปิดเผย public key โดยตรงบนเชน โดยใช้ **HASH256 (SHA-256 สองครั้ง)** ของ public key เพื่อลดขนาดเอาต์พุตและเพิ่มความปลอดภัย Hash นี้ทำหน้าที่เป็น cryptographic commitment ขั้นต่ำต่อ public key ในรูปแบบของ BIP-141 witness program

**รายละเอียดการดำเนินการ**&#x20;

* **Descriptor Format:** แนะนำฟังก์ชัน descriptor ใหม่ `qrh()` เพื่อรวม P2QRH เข้ากับ wallet software ที่มีอยู่ ฟังก์ชันนี้จะระบุ threshold, key type (secp256k1, FALCON-512, CRYSTALS-Dilithium, SPHINCS+-128s) และ HASH256 hashes ของ public key ที่เกี่ยวข้อง
  * สามารถใช้ public key hashes แทน raw public keys ใน descriptor ได้
  * ในการใช้จ่าย สามารถเลือกเปิดเผย public key ได้ โดยบาง key สามารถซ่อนไว้ได้โดยใช้เพียง hash
  * อย่างน้อยต้องมี key type สองประเภทใน P2QRH output คือ การเข้ารหัสแบบคลาสสิกหนึ่ง key และ PQC algorithm หนึ่ง key
  * ลำดับของ keys และ hashes ใน descriptor มีความสำคัญและอิงตามค่า public key ดั้งเดิมและ key type
  * `qrh()` ไม่ได้คอมไพล์เป็น script แต่ใช้อธิบายสิ่งที่จำเป็นในการคำนวณ scriptPubKey hash commitment และเปิดเผย attestation ที่จำเป็นในการใช้จ่าย output
* **Address Format:** ใช้ SegWit version 3 ขึ้นต้นด้วย `bc1r` เป็น Bech32m-encoded HASH256 ขนาด 32 ไบต์ของ HASH256 ของ public keys
* **ScriptPubKey:** รูปแบบคือ `OP_PUSHNUM_3 OP_PUSHBYTES_32 <hash>` โดย `<hash>` คือ HASH256 ขนาด 32 ไบต์ของ commitment
  * **Key Type Bitmask:** ค่า 1 ไบต์ที่บ่งบอก key type ที่ใช้ใน commitment (0x01 สำหรับ secp256k1, 0x02 สำหรับ FALCON, 0x04 สำหรับ Dilithium, 0x08 สำหรับ SPHINCS+)
  * **Hash Commitment:** คำนวณโดย:&#x20;
    * 1\. จัดเรียง public keys ตาม key type และค่า public key&#x20;
    * 2\. คำนวณ HASH256 ของแต่ละ public key ที่จัดเรียงแล้ว&#x20;
    * 3\. นำ hashes มาต่อกัน&#x20;
    * 4\. นำ key type bitmask และ threshold มานำหน้า hashes ที่ต่อกัน&#x20;
    * 5\. คำนวณ HASH256 ของผลลัพธ์
  * สำหรับ single public key การ hash คือ HASH256 ของ public key
  * รองรับ Taproot Merkelized Alternative Script Tree (MAST) merkle root ใน commitment ทำให้ P2QRH เป็นเวอร์ชันที่ทนทานต่อควอนตัมของ Taproot transactions
* **Transaction Serialization:** รูปแบบ transaction ใหม่มี field `attestation` เพิ่มเข้ามาหลัง field `witness`
  * `marker`: 0x00 (เหมือน SegWit)
  * `flag`: 0x02 (มีเฉพาะ attestation) หรือ 0x03 (มีทั้ง witness และ attestation)
  * `attestation`: มี public key และลายเซ็นที่ทนทานต่อควอนตัม
* **Quantum Transaction ID (qtxid):** Transaction ID คำนวณเป็น HASH256 ของ serialized transaction รวมถึง attestation และ witness (ถ้ามี) เมื่อถอดรหัส จะเรียกว่า qtxid ซึ่งจะแตกต่างจาก txid และ wtxid หากมี attestation
* **Attestation Structure:** ประกอบด้วย key\_type\_bitmask, threshold, num\_pubkeys, และสำหรับแต่ละ public key จะมี key\_type, pubkey\_length, และ pubkey และสำหรับแต่ละ signature จะมี signature\_length และ signature โครงสร้างนี้จะซ้ำสำหรับแต่ละ input
  * สามารถเปิดเผย public key hash ที่ไม่ต้องการใช้ด้วยลายเซ็นว่างเปล่า (signature length 0x00) เพื่อพิสูจน์ว่าเป็นส่วนหนึ่งของ commitment เดิม
* **Signature Algorithms:** รองรับ secp256k1 (Schnorr), FALCON-512, CRYSTALS-Dilithium Level I, และ SPHINCS+-128s Key type bitmask จะบ่งบอกว่า algorithm ใดถูกใช้สำหรับแต่ละ key FALCON signatures มีขนาดประมาณ 20 เท่าของ Schnorr signatures SPHINCS+ signatures มีขนาดใหญ่ที่สุด
* **Script Validation:** ต้องตรงตามรูปแบบ scriptPubKey, attestation ต้องมี public key (หรือ hash) และลายเซ็นที่ถูกต้องตรงกับ commitment, key type bitmask และ threshold ต้องตรงกับ commitment และสำหรับ multisig ต้องมี public key และลายเซ็นที่จำเป็นเพียงพอตาม threshold
* **Sighash Calculation:** คำนวณตามขั้นตอนเดียวกับ BIP-341 สำหรับ Taproot transactions
* **Compatibility with BIP-141:** Nodes ที่ไม่รู้จัก SegWit version 3 จะถือว่า output นี้เป็น anyone-can-spend แต่จะไม่ relay หรือ mine transaction ดังกล่าว

**ข้อควรพิจารณาในการใช้งาน (Usage Considerations)**

* **ขนาด Transaction และค่าธรรมเนียม:** ลายเซ็นที่ทนทานต่อควอนตัมมีขนาดใหญ่กว่ามาก ทำให้ transaction มีขนาดใหญ่ขึ้นและต้องการค่าธรรมเนียมสูงขึ้น
* **ผลกระทบต่อประสิทธิภาพ:** การยืนยันลายเซ็นที่ทนทานต่อควอนตัมจะใช้การคำนวณที่มากกว่า และ attestation discount ก็จะเพิ่มความต้องการในการจัดเก็บข้อมูล
* **การเลือก Algorithm:** มี algorithm สามแบบให้ผู้ใช้เลือกตามความเหมาะสมของกรณีการใช้งาน โดยทั่วไปจะอิงตามมูลค่าที่ต้องการรักษาความปลอดภัย
* **Backward Compatibility:** Wallet และ node รุ่นเก่าที่ไม่รองรับ SegWit version 3 และ P2QRH จะไม่รู้จัก output เหล่านี้

**ความปลอดภัย (Security)**

ขนาดลายเซ็นที่ใหญ่ขึ้นให้ความปลอดภัยที่มากขึ้น NIST ได้กำหนดระดับความปลอดภัยห้าระดับสำหรับ post-quantum cryptography โดยระดับ I ให้ความปลอดภัยเทียบเท่า 128-bit keys และระดับ V ให้ความปลอดภัย 256-bit security

