---
description: 'Right Tech EP.5: วิวัฒนาการของ Bitcoin Address | จาก IP Address ถึง Bech32m'
---

# Right Tech EP.5

{% embed url="https://www.youtube.com/watch?v=VoOf4Te0ry4" %}

## History of Address

### 1. IP address {P2PK (pay to public key)}

การที่เราจะส่ง Bitcoin ให้กันได้ เราต้องรู้ public key ของคนที่เราต้องการจะส่งให้ คล้ายกับการโอนเงินผ่านบัญชีธนาคารที่เราต้องรู้หมายเลขบัญชีธนาคาร แต่ปัญหาคือ public key มีความยาวมาก ทำให้ไม่สะดวกต่อการส่งต่อผ่านการพูดหรือสื่อสารด้วยเสียง

```
x = F028892BAD7ED57D2FB57BF33081D5CFCF6F9ED3D3D7F159C2E2FFF579DC341A
y = 07CF33DA18BD734C600B96A72BBC4749D5141C90EC8AC328AE52DDFE2E505BDB
```

จึงมีการแก้ปัญหาอยู่ 2 วิธี

1.  ส่งด้วย IP address (Bitcoin Full node version 0.1)

    ในยุคเริ่มต้นของ Bitcoin หากเราต้องการส่ง Bitcoin ให้ใคร สิ่งที่ต้องทำคือการรัน **Node** ขึ้นมาก่อน เพราะในช่วงแรก Node จะทำหน้าที่หลายอย่างในเวลาเดียวกัน ทั้งการขุดเหรียญ การทำหน้าที่เป็นกระเป๋าเงิน (wallet) และอื่น ๆ

    เมื่อรัน Node เรียบร้อยแล้ว เราต้องให้ Node ของเราทำการเชื่อมต่อ (peer) เพื่อค้นหา Node ของผู้รับ Bitcoin จากนั้นให้นำ **IP Address ของผู้รับ** มาใส่ไว้ในระบบของ Node ผู้ส่ง Node ของเราจะใช้ IP Address นี้เพื่อสอบถามไปยัง Node ของผู้รับว่า **public key ของผู้รับคืออะไร**

    การทำงาน:\
    Sender: `<Receiver Pubkey> OP_HECKSIG` (output)\
    Receiver: `<Receiver Signature>` + `output Sender` มาวางเป็น (input)

    Execution Stack: `<Receiver Signature> <Receiver Pubkey> OP_HECKSIG`

    ```
    Stack เริ่มต้น: []

     -> Push <Signature>
     Stack: [sig]

     -> Push <PublicKey>
     Stack: [sig, pubkey]

     -> Execute OP_CHECKSIG
        - ใช้ค่าบนสุด 2 ค่าใน stack (sig, pubkey)
        - ตรวจสอบว่า sig เป็นลายเซ็นที่ถูกต้องของธุรกรรมนี้หรือไม่ (ภายใต้ pubkey)

     -> ถ้าถูกต้อง: Stack = [true]
    ```

\
ปัญหาของการส่งด้วย Ip address

* ปิด computer ในเวลากลางคืน
* computer ของผู้รับอยู่ใน mode sleep
* อยู่หลัง file wall
* ใข้การแปลงที่อยู่เครือข่าย (NAT)
* ความปลอดภัยและความเป็นส่วนตัว

2.  Hash เพื่อให้สั้นลง (Legacy addresses)\
    เนื่องจาการ SHA-256 จะทำให้ ผลลัพธ์ของการ Hash เป็น 256 bit เสมอ (256 bit)\
    ใน public key hash ใช้ SHA-256 และ ตามด้วย RIPEMD-160 (ผลลัพธ์ 160 bit -> 20 byte -> 40 ตัวอักษร)

    การทำงาน:\
    Sender: `OP_DUP OP_HASH160 <Receiver commitment> OP_EQUALVERIFY OP_CHECKSIG`\
    Receiver: `<Receiver Signature> <Receiver PublicKey>`

    > \[!NOTE] > คือ public key ที่ผ่าน Hahs-160 (SHA-256 + RIPEMD-160)

    Execution Stack:

    `<Signature> <PublicKey> OP_DUP OP_HASH160 <PubKeyHash> OP_EQUALVERIFY OP_CHECKSIG`

    ```

    Stack เริ่มต้น: []

    -> Push <Signature> จาก Receiver
    Stack: [sig]

    -> Push <PublicKey> จาก Receiver
    Stack: [sig, pubkey]

    -> Execute OP_DUP
    	- ทำซ้ำค่า pubkey บน top ของ stack
    Stack: [sig, pubkey, pubkey]

    -> Execute OP_HASH160
    	- ทำ Hash160 (SHA-256 + RIPEMD-160) กับ pubkey บน top
    Stack: [sig, pubkey, pubkey_hash]

    -> Push <PubKeyHash> จาก Sender
    Stack: [sig, pubkey, pubkey_hash, expected_pubkey_hash]

    -> Execute OP_EQUALVERIFY
    	- ตรวจสอบว่า pubkey_hash ตรงกับ expected_pubkey_hash หรือไม่
    	- ถ้าไม่ตรง -> ธุรกรรมล้มเหลว
    	- ถ้าตรง -> ลบสองค่านั้นออกจาก stack
    Stack: [sig, pubkey]

    -> Execute OP_CHECKSIG
    	- ตรวจสอบว่า sig เป็นลายเซ็นของธุรกรรมนี้ที่ถูกต้องภายใต้ pubkey หรือไม่
    	- ถ้าถูกต้อง
    Stack: [true]
    ```

***

### 2. Base58check Encoding

**base58 check**: ถูกดัดแปลงมาจาก **base64** (เลขฐาน 64: 0-9, A-Z, a-z, +, /)\
โดยตัดสัญลักษณ์ที่อาจทำให้เกิดความสับสน เช่น +, /, 0 (เลขศูนย์), O (ตัวอักษร O), I (ตัวอักษร ไอใหญ่) และ l (ตัวอักษร เอลเล็ก) ออกไป\
และ เติม **prefix** และ **checksum** เข้าไป

**prefix**: เพื่อบอกว่า address นี้เป็นประเภทไหน

**checksum**: เพื่อ check ว่า ที่เรา coppy address มาถูกต้องหรือไม่

การทำงานของ base58 check encoding:

1. เตรียม payload : Script Sender (`OP_DUP OP_HASH160 <Receiver commitment> OP_EQUALVERIFY OP_CHECKSIG`)\
   `<payload>`
2. เติม prefix (version):\
   `<version> <payload>`
3. Hash (prefix + payload) และนำ 4 byte แรกมาต่อท้ายเป็น checksum:\
   `<version> <payload> <checksum>`
4. encode in base58

| ประเภท                       | version prefix (HEX) | output (Base58) |
| ---------------------------- | -------------------- | --------------- |
| address ประเภท P2PKH         | 0x00                 | 1               |
| address ประเภท P2SH          | 0x05                 | 3               |
| testnet address ประเภท P2PKH | 0x6F                 | m หรือ n        |
| testnet address ประเภท P2SH  | 0xc4                 | 2               |
| Private key WIF              | 0x80                 | 5, K หรือ L     |
| BIP32 Extended public key    | 0x0488B21E           | xpub            |

### summary Public key to Bitcoin Address:

![Public key to Bitcoin address: conversion of a public key to a Bitcoin address.](assets/2025-07-08-14-27-41.png)

***

### 4. Legacy:Pay to Script Hash (P2SH)

update in BIP 16 : อนุญาตให้ commitment ใส่ redeem script ลงไปแทนได้

ตัวอย่างการทำงานของ P2SH: (multi-signature ใช้ 2 key ในการไข)

redeem script: `<public key 1> OP_CHECKSIGVERIFY <public key 2> OP_CHECKSIG`

> \[!NOTE] OP\_CHECKSIGVERIFY ถ้าตรวจสอบแล้วถูกต้องจะ return 1 และ stack จะทำงานต่อ OP\_CHECKSIG ถ้าตรวจสอบแล้วถูกต้องจะ return true และ valid เลย

เราสามารถ ใส่ redeem script ลงไปใน commitment

Sender: `OP_HASH160 <commitment> OP_EQUAL`

Receiver: `<signature 2> <signature 1>` + `output Sender ( redeem script)`<br>

Execution Stack:\
`<signature 2> <signature 1> <public key 1> OP_CHECKSIGVERIFY <public key 2> OP_CHECKSIG`

collision attack on P2SH:<br>

* first preimage attack:<br>
* second preimage attack:<br>
* colision attack:<br>
  * หากผู้โจมตีเป็นคนที่ มี signature 1 ใน 2 ความปลอดภัย จาก $2^{160}$ จะเหลือ $2^{80}$ เท่านั้น ด้วน ตัเลข และทรัพยากรที่มีอยู่ในปัจจุบัน หากมีคนลงทุกสามารถทำได้

***

### 5. Bech32 Addresses

#### 5.1 ปัญหาของ BASE58CHECK

* อักษรที่มีทั้งตัวพิมพ์ใหญ่และตัวพิมพ์เล็กทำให้ไม่สะดวกในการอ่านออกเสียง
* ตรวจจับข้อผิดพลาดได้ แต่ไม่สามารถช่วยผู้ใช้แก้ไขข้อผิดพลาดได้
* การใช้ตัวอักษรที่มีทั้งตัวพิมพ์ใหญ่และตัวพิมพ์เล็กยังต้องใช้พื้นที่เพิ่มเติมในการเข้ารหัสใน QR code

#### 5.2 ปัญหาของ Bech 32

ค่าคงที่ตัวหนึ่งในอัลกอริทึม bech32 บังเอิญทำให้การเพิ่มหรือลบตัวอักษร "q" ในตำแหน่งที่สองจากท้ายของ address ที่ลงท้ายด้วยตัวอักษร "p" ในกรณีนี้ ทำให้สามารถเพิ่มหรือลบตัวอักษร "q" ได้โดยที่ checksum ยังคงถูกต้อง

```
address: ที่ถูกต้อง
bc1pqqqsq9txsqp

address: ที่ไม่ถูกต้อง
bc1pqqqsq9txsqqqqp
bc1pqqqsq9txsqqqqqqp
bc1pqqqsq9txsqqqqqqqqp
bc1pqqqsq9txsqqqqqqqqqp
bc1pqqqsq9txsqqqqqqqqqqqp

```

***

### 6. Bech32m Addresses

* **Bech32m address**:
  * เริ่มต้นด้วย **ส่วนที่มนุษย์อ่านได้ (HRP: Human-Readable Part)**
  * ตามด้วย **ตัวคั่นคือเลข "1"**
  * ส่วนข้อมูลหลังตัวคั่นแบ่งเป็น 3 ส่วน: `witness version`, `witness program`, และ `checksum`
* **Witness version**:
  * ไบต์ถัดจากตัวคั่น (1) ใช้ระบุเวอร์ชันของ SegWit
  * ตัวอักษร `"q"` แทนค่า `0` → ใช้กับ **SegWit v0** (เวอร์ชันแรกของ bech32)
  * ตัวอักษร `"p"` แทนค่า `1` → ใช้กับ **SegWit v1 (Taproot)**
  * รองรับเวอร์ชันตั้งแต่ 0 ถึง 16 (รวมทั้งหมด 17 เวอร์ชัน)
* **Witness program**:
  * เริ่มจาก Byte 2 to 40 (หลัง witness version)
  * สำหรับ **SegWit v0** → ความยาวต้องเป็น **20 หรือ 32 ไบต์** เท่านั้น
  * สำหรับ **SegWit v1** → ความยาวต้องเป็น **32 ไบต์** (ณ ปัจจุบัน)
  * ความยาวอื่นสำหรับ v1 อาจถูกกำหนดในอนาคต
* **Checksum**:
  * มีความยาว **6 ตัวอักษร**
  * ใช้ **BCH (Bech32 Checksum)** ซึ่งเป็นรหัสตรวจจับข้อผิดพลาด (Error Detection Code)

| Output Type    | Example Script                                                         | Bech32 Address (decode)                                          |
| -------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------------- |
| P2WPKH         | OP\_0 2b626ed108ad00a944bb2922a309844611d25468                         | `bc1q9d3xa5gg45q2j39m9y32xzvygcgay4rgc6aaee`                     |
| P2WSH          | OP\_0 648a32e50b6fb7c5233b228f60a6a2ca4158400268844c4bc295ed5e8c3d626f | `bc1qvj9r9egtd7mu2gemy28kpf4zefq4ssqzdzzycj7zjhk4arpavfhsct5a3p` |
| P2TR           | OP\_1 2ceefa5fa770ff24f87c5475d76eab519eda6176b11dbe1618fcf755bfac5311 | `bc1p9nh05ha8wrljf7ru236awm4t2x0d5ctkkywmu9sclnm4t0av2vgs4k3au7` |
| Future Example | OP\_16 0000                                                            | `bc1sqqqqkfw08p`                                                 |

***
