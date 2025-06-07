---
description: "เลือก Bitcoin Wallet ยังไงให้ปลอดภัย? | Right Tech EP.4 (อ.อาร์ม x อิสร x มูฟวี่)"
---

# Right Tech EP.4

{% embed url="https://www.youtube.com/watch?v=cK0I4WsfwP4&t=1409s" %}

## Don't trust, verify. Verify

เราจะตรวจสอบอย่างไรว่าเป็นโปรแกรม Software wallet ของแท้ ไม่หลอก ไม่ลวง

---

### การติดตั้ง GPG

> เพื่อใช้ในการ verify key และ signature ของโปรแกรม Wallet ที่เราจะติดตั้ง

#### macOS

**ขั้นตอนที่ 1:** ติดตั้ง GPG ผ่าน Homebrew

```bash
brew install gnupg
```

**ขั้นตอนที่ 2:** ตรวจสอบการติดตั้ง

```bash
gpg --version
```

**ผลลัพธ์ที่คาดหวัง:**

```bash
gpg --version
gpg (GnuPG) 2.4.7
libgcrypt 1.11.0
Copyright (C) 2024 g10 Code GmbH
License GNU GPL-3.0-or-later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Home: /Users/phoovich/.gnupg
Supported algorithms:
Pubkey: RSA, ELG, DSA, ECDH, ECDSA, EDDSA
Cipher: IDEA, 3DES, CAST5, BLOWFISH, AES, AES192, AES256, TWOFISH,
        CAMELLIA128, CAMELLIA192, CAMELLIA256
Hash: SHA1, RIPEMD160, SHA256, SHA384, SHA512, SHA224
Compression: Uncompressed, ZIP, ZLIB, BZIP2
```

#### Windows

**ขั้นตอนที่ 1:** ดาวน์โหลด GPG4Win

เยี่ยมชม [gpg4win.org](https://www.gpg4win.org/) และดาวน์โหลดไฟล์ติดตั้ง

**ขั้นตอนที่ 2:** ตรวจสอบการติดตั้ง

เปิด PowerShell และรันคำสั่ง:

```bash
gpg --version
```

**ผลลัพธ์ที่คาดหวัง:**

```bash
gpg (GnuPG) 2.4.8
libgcrypt 1.11.1
Copyright (C) 2025 g10 Code GmbH
License GNU GPL-3.0-or-later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Home: C:\Users\phoovich\AppData\Roaming\gnupg
Supported algorithms:
Pubkey: RSA, ELG, DSA, ECDH, ECDSA, EDDSA
Cipher: IDEA, 3DES, CAST5, BLOWFISH, AES, AES192, AES256, TWOFISH,
        CAMELLIA128, CAMELLIA192, CAMELLIA256
Hash: SHA1, RIPEMD160, SHA256, SHA384, SHA512, SHA224
Compression: Uncompressed, ZIP, ZLIB, BZIP2
```

---

### การเลือก Wallet

มีกระเป๋าเงินบิตคอยน์หลากหลายประเภทให้เลือกใช้ แต่ละแบบมีจุดเด่นและข้อจำกัดที่แตกต่างกัน ควรศึกษาข้อมูลและเปรียบเทียบความเหมาะสมกับการใช้งานของคุณ

สำหรับข้อมูลเปรียบเทียบและคำแนะนำ: [https://bitcoin.org/en/choose-your-wallet](https://bitcoin.org/en/choose-your-wallet)

---

### Verify Bitcoin Wallets

> [!IMPORTANT]
> บทความนี้ใช้ Sparrow Wallet เป็นตัวอย่างในการตรวจสอบความถูกต้อง Wallet อื่น ๆ สามารถใช้วิธีการเดียวกันได้
>
> ไม่ใช่คำแนะนำในการเลือก Wallet ท่านควรตัดสินใจและเลือก Wallet ที่เหมาะสมกับท่านเอง
>
> การตรวจสอบความถูกต้องของ Wallet เป็นสิ่งสำคัญเพื่อป้องกันการถูกหลอกลวงหรือการใช้ซอฟต์แวร์ที่ไม่ปลอดภัย

### Verify Sparrow Wallet

> คุณสามารถดูวิธีการตรวจสอบความถูกต้องของ Sparrow Wallet ได้ที่ [https://sparrowwallet.com/download/](https://sparrowwallet.com/download/)

**1. check link ว่าเป็น official website หรือไม่**

**2. download file signature และ sparrow wallet**

- sparrow wallet (.msi, .dmg, .tar.gz, .deb)
- Manifest signature (.txt.asc)
- Manifest (.txt)

> คำแนะนำ: ควรดาวน์โหลดไฟล์นี้ไว้ในโฟลเดอร์เดียวกันเพื่อง่ายแก่การตรวจสอบ

**3. Import Key**

```bash
curl https://keybase.io/craigraw/pgp_keys.asc | gpg --import
```

**ผลลัพธ์ที่คาดหวัง:**

```bash
gpg: key E94618334C674B40: "Craig Raw <craig@sparrowwallet.com>" not changed
gpg: Total number processed: 1
gpg:              unchanged: 1
```

**หากพบข้อความ:**

```bash
gpg: no valid OpenPGP data found.
gpg: Total number processed: 0
```

**ให้ download key จาก https://keybase.io/craigraw/pgp_keys.asc โดยตรง เเล้ว import ใหม่**

```bash
gpg --import pgp_keys.asc(หรือชื่อไฟล์ที่คุณบันทึกไว้)
```

**ผลลัพธ์ที่คาดหวัง:**

```bash
gpg: key E94618334C674B40: public key "Craig Raw <craig@sparrowwallet.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
```

**4. verify signature**

```bash
gpg --verify spsparrow-2.2.2-manifest.txt.asc
```

**ผลลัพธ์ที่คาดหวัง:**

```bash
gpg: assuming signed data in 'sparrow-2.2.2-manifest.txt'
gpg: Signature made Thu May 22 19:11:35 2025 +07
gpg:                using RSA key D4D0D3202FC06849A257B38DE94618334C674B40
gpg: Good signature from "Craig Raw <craig@sparrowwallet.com>" [unknown] <--- signature
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: D4D0 D320 2FC0 6849 A257  B38D E946 1833 4C67 4B40 <--- key
```

**5. นำข้อความที่ได้ไปเปรียบเทียบกับหน้าเว็บ**

```bash
gpg: assuming signed data in 'sparrow-2.2.2-manifest.txt'
gpg: Signature made Thu May 22 14:11:35 2025 SAST
gpg:                using RSA key D4D0D3202FC06849A257B38DE94618334C674B40 <--- ตรวจสอบว่า key ตรงกับที่เรา import หรือไม่
gpg: Good signature from "Craig Raw <craig@sparrowwallet.com>" [unknown] <--- ตรวจสอบว่าเป็น signature ของ Craig Raw หรือไม่
```

**6. ตรวจสอบ hash ของไฟล์ sparrow wallet**

> สามารถดูคำสั่งได้ที่หน้าเว็บของ Sparrow Wallet

**macOS:**

```bash
shasum --check sparrow-2.2.2-manifest.txt --ignore-missing
```

**Windows:**

```bash
CertUtil -hashfile Sparrow-2.2.2.msi SHA256 | findstr /v "hash"
```

**ผลลัพธ์ที่คาดหวัง:**

```bash
a03623fd37996d8d82e1833f96b7515a0c2ce0722648d986dce79ab1c7d00a35 <-- hash ของ sparrow wallet
```

**7. ตรวจสอบว่า hash ตรงกับในไฟล์ manifest หรือไม่**

```bash
cat sparrow-2.2.2-manifest.txt
cat sparrow-2.2.2-manifest.txt
268feb276e1d040db8d6febb647790e003567edf78910d61f125586aa3253fa4 *Sparrow-2.2.2-aarch64.dmg
a8f21b09280ee4a6a7c4433de94460747bf5c53cc84a790c08b0049689d62b6c *Sparrow-2.2.2-x86_64.dmg
a03623fd37996d8d82e1833f96b7515a0c2ce0722648d986dce79ab1c7d00a35 *Sparrow-2.2.2.msi <---- ตรวจสอบว่า hash ตรงกับที่เราได้จากขั้นตอนที่ 6 หรือไม่
                                                        ⋮
a5a9be550ddbc32287d00822c809021135f936801f95c4fae1c481fea16ee51b *sparrowwallet-2.2.2-x86_64.tar.gz
9c06a77e7a1929af4d50c48128475204e43d16129ae5e098ab605793a3aac78a *sparrowwallet_2.2.2-1_amd64.deb
521ffd825b16df3a048c5a6083d819594772cec7cc8c4055158e2e01f14bdd5d *sparrowwallet_2.2.2-1_arm64.deb
```
