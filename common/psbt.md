---
icon: pencil
---

# PSBT คืออะไร และแก้ปัญหาอะไร

{% embed url="https://youtu.be/i1tD65mEhzU" %}
เนิื้อหาในคลิปมีการใช้ AI ในการสร้างเสียง และเนื้อหาไม่ครบถ้วนเท่าบทความด้านล่าง จัดทำเพื่อใครที่ต้องการทำความเข้าใจแบบพื้นฐานก่อนจะลงลึก
{% endembed %}

**PSBT (Partially Signed Bitcoin Transaction)** เป็นฟอร์แมตรูปแบบข้อมูลสำหรับธุรกรรมของบิตคอยน์ที่ออกแบบมาเพื่อให้โปรแกรมหรือกระเป๋าเงินหลายตัวสามารถแลกเปลี่ยนข้อมูลของธุรกรรมและลายเซ็นบางส่วนระหว่างกันได้ PSBT ได้ช่วยแก้ปัญหาการทำธุรกรรมที่ต้องมีหลายคนเซ็น (multi-signature) หรือกรณีที่ใช้กระเป๋าเงินหลายประเภทร่วมกัน โดยไม่ต้องเปิดเผยข้อมูลภายในกันโดยตรง นอกจากนี้ PSBT ยังอนุญาตให้อุปกรณ์ออฟไลน์หรือฮาร์ดแวร์วอลเล็ตลงชื่อได้โดยไม่ต้องเชื่อมต่อกับชุด UTXO ทั้งหมด ทำให้ปลอดภัยต่อการใช้งานบน cool wallet ในทางเทคนิค PSBT มีการกำหนดตาม BIP174 (เวอร์ชัน 0) และมี BIP370 สำหรับ PSBT เวอร์ชัน 2 ที่เพิ่มฟีเจอร์ใหม่ ๆ (เช่นข้อมูล Taproot)

PSBT จะเริ่มจากการเตรียมธุรกรรมเปล่า (unsigned transaction) ที่ระบุ UTXO ที่จะใช้จ่ายและ address ปลายทาง จากนั้นข้อมูลสำคัญของแต่ละ input เช่น สคริปต์และจำนวนเงินของ UTXO นั้น ๆ จะถูกเพิ่มเข้าไปใน PSBT และสามารถส่งต่อไปยังโปรแกรมหรืออุปกรณ์อื่นเพื่อใช้เซ็นลายเซ็นได้ โดยแต่ละรอบจะใส่ลายเซ็นบางส่วนเข้าไปใน PSBT หากเป็น wallet multisig หรือต้องใช้หลายคีย์ในการเซ็น ก็สามารถส่ง PSBT สำเนาให้แต่ละคนลงชื่อทีละคน และสุดท้ายรวม PSBT เหล่านั้นให้ได้ลายเซ็นครบถ้วน จากนั้นจึงแปลง PSBT ที่ลงชื่อครบเป็นธุรกรรมสมบูรณ์พร้อมประกาศสู่เครือข่ายได้

### โครงสร้างและประเภทของ Input/Output ใน PSBT

PSBT ถูกจัดเก็บในรูปแบบ _Key-Value Maps_ โดยแบ่งเป็นส่วน **global** map, **input** map และ **output** map&#x20;

* **Global Map**: ประกอบด้วยคีย์ข้อมูลทั่ว ๆ ไป เช่น ธุรกรรมบิทคอยน์ที่ยังไม่เซ็น (`PSBT_GLOBAL_UNSIGNED_TX`) ในรูปแบบไบนารีของ Transaction (scriptSig และ witness ของทุก input ) และสามารถมี public keys ตาม BIP32 (XPUB) เพื่อถ่ายทอดข้อมูลคีย์ต้นทางได้ (PSBT\_GLOBAL\_XPUB)
* **Input Map (แต่ละ input)**: มีข้อมูลเฉพาะของแต่ละ input โดยรองรับหลายประเภท ได้แก่：
  * **Non-Witness UTXO (PSBT\_IN\_NON\_WITNESS\_UTXO)**: ถ้า input ใช้ UTXO แบบเดิม (non-segwit) จะเก็บทั้ง transaction เดิมไว้ทั้งหมด&#x20;
  * **Witness UTXO (PSBT\_IN\_WITNESS\_UTXO)** เก็บแค่ output เดิม (จำนวนเงินและ scriptPubKey) สำหรับ input แบบ SegWit
  * **ลายเซ็นบางส่วน (PSBT\_IN\_PARTIAL\_SIG)**: เก็บคู่ `(Public Key, Signature)` ที่เซ็นโดยผู้ร่วมเซ็นแต่ละคน
  * **Sighash Type (PSBT\_IN\_SIGHASH\_TYPE)**: ระบุวิธีการคำนวณแฮชสำหรับการเซ็น input นั้น (เช่น ALL, SINGLE ฯลฯ)
  * **Redeem Script (PSBT\_IN\_REDEEM\_SCRIPT)**, **Witness Script (PSBT\_IN\_WITNESS\_SCRIPT)**: กรณี output เดิมเป็น P2SH หรือ P2WSH จะเก็บสคริปต์เหล่านั้นไว้เพื่อใช้สร้างลายเซ็น
  * **BIP32 Derivation (PSBT\_IN\_BIP32\_DERIVATION)**: ข้อมูลเส้นทางการสืบทอดกุญแจ (master fingerprint + derivation path) ของ public key ใน transaction input เพื่อให้กระเป๋าเงินสามารถสืบหาคีย์ส่วนตัวได้
  * **ScriptSig/ScriptWitness (PSBT\_IN\_FINAL\_SCRIPTSIG, PSBT\_IN\_FINAL\_SCRIPTWITNESS)**: เมื่อเซ็นเสร็จ สมบูรณ์ จะเก็บ scriptSig หรือ witness เต็มสำหรับ input เพื่อให้ตรวจสอบถูกต้องได้
* **Output Map (แต่ละ output)**: เก็บข้อมูลที่เกี่ยวข้องกับแต่ละ output ได้แก่
  * **Redeem Script (PSBT\_OUT\_REDEEM\_SCRIPT)** และ **Witness Script (PSBT\_OUT\_WITNESS\_SCRIPT)**: หาก output เป็น P2SH/P2WSH ใด ๆ ก็เก็บสคริปต์ไว้สำหรับอ้างอิง
  * **BIP32 Derivation (PSBT\_OUT\_BIP32\_DERIVATION)**: กรณี output เป็นการจ่ายให้ address ที่เป็น HD wallet เก็บข้อมูลเส้นทางคีย์ (key origin) ของ public key ที่สร้าง output นี้
  * **(PSBTv2)** ใน PSBT เวอร์ชัน 2 จะมีฟิลด์ **PSBT\_OUT\_AMOUNT** และ **PSBT\_OUT\_SCRIPT** เก็บจำนวนเงินและ scriptPubKey ของแต่ละ output (ฟิลด์นี้ไม่มีใน PSBTv0)
  * **(PSBTv2 Taproot)** ถ้า output เป็น Taproot จะมีฟิลด์เก็บ X-only internal key และโครงสร้างต้นไม้ Taproot ต่าง ๆ เช่น `PSBT_OUT_TAP_INTERNAL_KEY`, `PSBT_OUT_TAP_TREE`, `PSBT_OUT_TAP_BIP32_DERIVATION` เพื่อรองรับ Taproot (ข้อนี้เป็นส่วนเสริมล่าสุดใน PSBTv2)

โดยสรุป **PSBT** ใช้โครงสร้าง _map_ นี้เพื่อรองรับ input/output ทุกประเภทของ Bitcoin (P2PKH, P2SH, P2WPKH, P2WSH, P2TR ฯลฯ) โดยเก็บข้อมูลสคริปต์และคีย์ที่จำเป็นไว้ในรูปแบบยืดหยุ่น

### ขั้นตอนการสร้างและเซ็น PSBT (Workflow)

กระบวนการทำงานของ PSBT แบ่งเป็นหลายบทบาทตามที่กำหนดใน BIP174, ได้แก่ **Creator**, **Updater**, **Signer**, **Combiner**, **Finalizer** และ **Extractor** สรุปขั้นตอนดังนี้:

* **Creator (สร้าง PSBT)**: เริ่มจากสร้าง Transaction เปล่า (มีรายการ input/output แต่ยังไม่มีลายเซ็นและ scriptSig/witness) แล้วสร้าง PSBT จากธุรกรรมนั้น (เช่นใน Rust ใช้ `Psbt::from_unsigned_tx`)
* **Updater (เติมข้อมูล)**: ใส่ข้อมูลเฉพาะของแต่ละ input เช่น UTXO เก่า (ใส่ใน `non_witness_utxo` หรือ `witness_utxo`), scriptPubKey/RedeemScript, ข้อมูล path ของ HD key, ชนิด sighash ฯลฯ ข้อมูลเหล่านี้ช่วยให้โปรแกรมเซ็นรู้ว่าต้องเซ็นอะไรและอย่างไร
* **Signer (ลงชื่อ)**: ผู้ถือคีย์ส่วนตัวจะทำการลงลายเซ็นใน PSBT สำหรับ input ที่เกี่ยวข้อง โดยการคำนวณ signature (เช่น ECDSA หรือ Schnorr) และใส่ลงในฟิลด์ `PSBT_IN_PARTIAL_SIG` ตามลำดับ หากมีหลายคนร่วมเซ็น สามารถส่ง PSBT สำเนาให้แต่ละคนเซ็นเพิ่มได้หลายรอบ
* **Combiner (รวม PSBT)**: หากได้มาหลาย PSBT จากคนละแหล่ง (เช่น multisig แต่ละคนเซ็นแยก) บทบาท Combiner จะนำ PSBT เหล่านั้นมารวมกันให้มีทุก signature อยู่ใน PSBT เดียว
* **Finalizer (สรุป)**: ตรวจสอบว่าแต่ละ input มี signature ครบตามที่ต้องการหรือไม่ จากนั้นสร้าง `scriptSig` หรือ `scriptWitness` เต็มรูป (final script) บน input แต่ละอัน แล้วลบข้อมูลชั่วคราวเช่น partial sig, sighash หรือ redeem/witness script ออกตามมาตรฐาน PSBT
* **Extractor (ดึง Transaction)**: เมื่อมี final script ครบ ก็สามารถแปลง PSBT เป็น Transaction ที่มีลายเซ็นครบถ้วน (Raw Transaction) พร้อมจะ broadcast ได้ (ใน rust-bitcoin ใช้ `psbt.extract_tx()` หรือ `consensus::encode::serialize_hex`)

ขั้นตอนเหล่านี้ช่วยให้การจัดการธุรกรรมที่ต้องมีหลายฝ่ายลงชื่อเป็นมาตรฐานและปลอดภัยขึ้น

### ตัวอย่างการใช้งาน PSBT ด้วย Rust

ตัวอย่างโค้ดต่อไปนี้ใช้ไลบรารี _rust-bitcoin_ ในการสร้าง PSBT, เติมข้อมูล input, และลงชื่อให้สมบูรณ์ (SegWit v0 example):

```
use bitcoin::{Transaction, TxIn, TxOut, OutPoint, Psbt, ScriptBuf};
use bitcoin::secp256k1::Secp256k1;

// สมมติเตรียม UTXO เดิมไว้
let prev_txid = ...; // TxID ของ utxo เก่า
let prev_out = 0;    // index ของ output เก่า
let utxo = TxOut { value: 50000, script_pubkey: prev_script.clone() };

// สร้าง transaction เปล่า (unsigned)
let unsigned_tx = Transaction {
    version: 2,
    lock_time: 0,
    input: vec![ TxIn {
        previous_output: OutPoint::new(prev_txid, prev_out),
        script_sig: ScriptBuf::new(),
        sequence: 0xFFFFFFFF,
        witness: Vec::new(),
    }],
    output: vec![ TxOut { value: 49000, script_pubkey: dest_script.clone() } ],
};

// สร้าง PSBT จาก transaction ที่ยัง unsigned
let mut psbt = Psbt::from_unsigned_tx(unsigned_tx).expect("Cannot create PSBT");

// เติมข้อมูลสำหรับ input ที่ PSBT
psbt.inputs[0].witness_utxo = Some(utxo.clone());                 // รายละเอียด UTXO เก่า
psbt.inputs[0].redeem_script = Some(prev_redeem_script.clone());  // ถ้ามี redeem script
psbt.inputs[0].bip32_derivation.insert(pubkey, (fingerprint, path)); // ข้อมูล HD key

// ลงชื่อ PSBT ด้วย private key
let secp = Secp256k1::new();
psbt.sign(&private_key, &secp).expect("Signing failed");

// สร้าง witness script สุดท้ายและดึง Transaction ที่เซ็นเสร็จ
psbt.finalize().expect("Finalizing failed");
let final_tx = psbt.extract_tx().expect("Extraction failed");
println!("Raw TX = {}", hex::encode(final_tx.serialize()));

```

โค้ดนี้ทำงานดังนี้:&#x20;

1. สร้าง `Transaction` ที่มีอินพุตและเอาต์พุตตามต้องการ แล้วใช้ `Psbt::from_unsigned_tx` สร้าง PSBT จากธุรกรรมนั้น
2. เติมข้อมูล input ได้แก่ UTXO เดิม (`witness_utxo`), สคริปต์ที่เกี่ยวข้อง (`redeem_script`), และเส้นทาง BIP32 (`bip32_derivation`)
3. ใช้ `.sign(&private_key, &secp)` เพื่อใส่ลายเซ็นลงใน PSBT
4. สุดท้ายเรียก .finalize() (หรือลงมือเขียน witness เอง) และ .extract\_tx() เพื่อดึง Transaction ที่มีลายเซ็นครบ สมบูรณ์พร้อมประกาศสู่เครือข่าย

**อ้างอิง:** เอกสาร BIP174 (PSBT) และบทความ Bitcoin Ops

* [https://bitcoinops.org/en/topics/psbt/](https://bitcoinops.org/en/topics/psbt/)
* [https://en.bitcoin.it/wiki/BIP\_0174](https://en.bitcoin.it/wiki/BIP_0174)

&#x20;รวมถึงตัวอย่างโค้ดจาก Rust Bitcoin Cookbook

* [https://rust-bitcoin.org/book/psbt.html](https://rust-bitcoin.org/book/psbt.html)
* [https://rust-bitcoin.org/book/psbt/multiple\_inputs\_segwit-v0.html](https://rust-bitcoin.org/book/psbt/multiple_inputs_segwit-v0.html)
