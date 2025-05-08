---
description: OP_Return คืออะไรทำไมถึงกลายเป็นประเด็นร้อนในวงการบิตคอยน์ตอนนี้
icon: rotate-right
---

# OP\_Return

**OP\_RETURN** เป็น opcode ในภาษา script ของ บิตคอยน์ที่เมื่อพบใน lock script จะทำให้สคริปต์สิ้นสุดทันทีและผลลัพธ์ของ output นั้นถือว่าไม่สามารถใช้จ่ายได้ (unspendable) [\[1\]](https://developer.bitcoin.org/reference/transactions.html) เนื่องจาก output ที่ใช้ OP\_RETURN จะไม่ถูกเพิ่มเข้าไปใน UTXO set จึงใช้สำหรับ “เผา” เหรียญหรือฝังข้อมูลเล็ก ๆ ลงบนบล็อกเชนได้โดยไม่สร้างภาระให้กลับ UTXO อื่น ๆ [\[2\]](https://en.bitcoin.it/wiki/OP_RETURN) แต่อย่างไรก็ดี เหล่านักพัฒนาได้ทำการระบุไว้ว่า การเก็บข้อมูลบนบล็อกเชนนั้นยังคงไม่ใช่จุดประสงค์หลักของ Bitcoin และเป็น “**ความคิดที่ไม่ดี**” ในทางปฏิบัติ [\[3\]](https://bitcoin.org/en/release/v0.9.0#opreturn-and-data-in-the-block-chain)

### จุดเริ่มต้นของการนำ OP\_RETURN มาใช้

ฟีเจอร์ OP\_RETURN ถูกเพิ่มใน **Bitcoin Core v0.9.0** ซึ่งออกเมื่อเดือนมีนาคม 2014 การเปลี่ยนแปลงนี้ทำให้สามารถสร้าง output ที่ใช้ OP\_RETURN (unspendable) ได้โดยตรง และทำให้ node ต่าง ๆ ทราบว่าข้อมูลใน OP\_RETURN ไม่จำเป็นต้องเก็บไว้ใน UTXO set [\[3\]](https://bitcoin.org/en/release/v0.9.0#opreturn-and-data-in-the-block-chain) ในตอนแรก Bitcoin Core จำกัดขนาดข้อมูลใน OP\_RETURN ที่สูงสุด 80 ไบต์ แต่ภายหลังลดเหลือ 40 ไบต์เพื่อป้องกันการบิดเบือนระบบ และต่อมาใน **Bitcoin Core v0.11.0 (ก.ค. 2015)**[ \[4\]](https://bitcoin.org/en/release/v0.11.0) ขยายขนาดกลับเป็น 80 ไบต์โดยค่าเริ่มต้น [\[5\] ](https://github.com/bitcoin/bitcoin/pull/5286)การเปลี่ยนแปลงเหล่านี้เป็นเพียงนโยบายของ relay (standardness rule) ไม่ใช่ consensus rule จึงมีผลต่อลักษณะการกระจายข้อมูลในเครือข่ายเท่านั้น [\[6\]](https://cryptoslate.com/bitcoin-cores-op_return-limit-removal-divides-crypto-community/)

### การใช้งานทั่วไปของ OP\_RETURN

* _แนบเมตาดาต้า (Metadata)_ เช่น การฝัง [hash ](common/Hash.md)ของเอกสารหรือข้อมูลภายนอกลงบนบล็อกเชน เพื่อใช้พิสูจน์การมีอยู่ของข้อมูล ณ เวลาใดเวลาหนึ่ง (proof of existence)  OP\_RETURN จึงถูกนำไปใช้ในระบบ timestamping หรือระบบจัดเก็บบันทึกดิจิทัลหลายโครงการ (เช่น OpenTimestamps) เพื่อให้มีหลักฐานยืนยันเวลาของข้อมูล [\[7\]](https://arxiv.org/abs/1702.01024)
  * Hash คืออะไร หากไม่เข้าใจสามารถอ่านได้จากบทความนี้ครับ --> <a href="common/Hash.md" class="button primary">บทความเรื่อง hash</a>
* _สร้างโทเค็นหรือ NFT บนบิตคอยน์_ ระบบนิเวศบางแห่งใช้ OP\_RETURN เพื่อฝังข้อมูลสร้างสินทรัพย์ดิจิทัล (token/NFT) ตัวอย่างเช่น โปรโตคอล Counterparty (เปิดตัว 2014), หรืออย่าง odinal ที่ใช้ OP\_RETURN เพื่อบันทึกการทำธุรกรรมของโทเค็นต่าง ๆ บนบล็อกเชนของบิตคอยน์  โครงการอย่าง Rare Pepe, Magic Cards และโทเค็นอื่น ๆ บน Bitcoin ก็ล้วนใช้ OP\_RETURN ในลักษณะนี้เป็นส่วนใหญ่ [\[7\]](https://arxiv.org/abs/1702.01024)
* _การพิสูจน์เวลา (Timestamping)_: สามารถนำ OP\_RETURN ไปแนบข้อมูลวันที่/เวลา หรือ hash ของข้อมูล เพื่อใช้สร้างลายเซ็นยืนยันเวลาของข้อมูลนั้น ๆ โดยไม่ต้องเชื่อใจหน่วยงานกลาง ตัวอย่างเช่น โครงการ OpenTimestamps มาตรฐานการพิสูจน์เวลาโดยใช้บล็อกเชน ใช้ OP\_RETURN เป็นกลไกส่งข้อมูลบันทึกลงในบล็อกเชนของบิตอคยน์ [\[6\]](https://cryptoslate.com/bitcoin-cores-op_return-limit-removal-divides-crypto-community/)
* _การเชื่อมโยงกับระบบภายนอก (Data anchoring)_: ใช้ OP\_RETURN เป็นสะพานบันทึกข้อมูลจากระบบอื่นเข้าสู่ Bitcoin เช่น ใช้ฝัง hash ของบล็อกเชนภายนอก หรือสถานะของฐานข้อมูลภายนอกลงบนบล็อกเชนของบิตคอยน์ ตัวอย่างเช่น โครงการ Exonum blockchain ใช้ OP\_RETURN ในธุรกรรมบิตคอยน์เพื่อบันทึก hash ของบล็อก Exonum ทำให้สามารถตรวจสอบความถูกต้องของข้อมูลภายนอกโดยอ้างอิงจากบล็อกเชนบิตคอยน์ได้ [\[8\]](https://exonum.com/doc/version/latest/advanced/bitcoin-anchoring/)

### ข้อจำกัดและข้อโต้แย้งด้านความปลอดภัยและประสิทธิภาพ

* _ขนาดข้อมูลจำกัด:_ ข้อมูลที่แนบใน OP\_RETURN ถูกจำกัดตามนโยบายการ relay ของ Bitcoin Core (เดิม 40 ไบต์ใน v0.9–0.10 และ 80 ไบต์ใน v0.11 ขึ้นไป) นโยบายเหล่านี้ทำให้แน่ใจได้ว่าแต่ละ output จะไม่รับภาระข้อมูลเกินควร โดยเฉพาะไม่สร้าง UTXO ที่ขนาดใหญ่ [\[1\]](https://developer.bitcoin.org/reference/transactions.html)[\[2\]](https://en.bitcoin.it/wiki/OP_RETURN)
* _ประเด็นความปลอดภัย:_ OP\_RETURN ทำให้ออปพุตนั้นกลายเป็น unspendable จึงไม่มีความเสี่ยงเรื่องการถูกโจรกรรม แต่มักถูกวิจารณ์ว่าเป็นช่องทางสำหรับฝังข้อมูลที่ไม่เกี่ยวกับการเงิน ซึ่งบางคนมองว่าเป็นการสแปมหรือก่อกวนเครือข่าย Bitcoin Core ได้ชี้แจงตั้งแต่ต้นว่า การเก็บข้อมูลบนบล็อกเชนด้วย OP\_RETURN มีความเสี่ยงและนโยบายนี้ไม่ได้สนับสนุนการใช้เครือข่ายเก็บข้อมูลนอกเหนือจากการทำธุรกรรมทางการเงิน [\[1\]](https://developer.bitcoin.org/reference/transactions.html)[\[6\]](https://cryptoslate.com/bitcoin-cores-op_return-limit-removal-divides-crypto-community/)
* _ประเด็นประสิทธิภาพ:_ แม้ว่าการใช้ OP\_RETURN จะไม่เพิ่มภาระต่อ UTXO set แต่ก็ยังเพิ่มขนาดของบล็อกเชนที่ต้องดาวน์โหลดและตรวจสอบ ผู้วิจัยบางกลุ่มเคยตั้งคำถามว่าการฝากข้อมูลบน OP\_RETURN เป็นจำนวนมากอาจทำให้เครือข่ายช้าลงหรือบล็อกเชนบวมขึ้น อย่างไรก็ตาม ฝ่ายสนับสนุนชี้ว่า Bitcoin Core ยังมีข้อจำกัดเรื่องขนาดบล็อก (4 ล้าน weight units) เงื่อนไข dust และนโยบายอื่น ๆ อยู่แล้ว จึงควบคุมปริมาณข้อมูลได้ระดับหนึ่ง [\[6\]](https://cryptoslate.com/bitcoin-cores-op_return-limit-removal-divides-crypto-community/) [\[7\]](https://arxiv.org/abs/1702.01024)
* _ข้อโต้แย้งล่าสุด:_ ในปี 2025 มีการถกเถียงในชุมชนของบิตคอยน์ เกี่ยวกับการผ่อนคลายข้อจำกัด OP\_RETURN โดยมีกลุ่มผู้พัฒนาเสนอให้ยกเลิกขนาด 80 ไบต์ ขณะที่ฝ่ายวิจารณ์มองว่าสิ่งนี้อาจกระทบกับธรรมชาติของบิตคอยน์  ฝ่ายสนับสนุน (อย่างเช่น Greg Sanders) ระบุว่าการยกเลิกนโยบายดังกล่าวจะช่วยให้นโยบายของ node สอดคล้องกับการใช้งานจริงและป้องกันการใช้วิธีการที่ซับซ้อนเกินไป ในขณะที่ผู้คัดค้าน (เช่น Luke Dashjr) ระบุว่าการปลดล็อกนั้นเป็น “ความบ้าคลั่ง” ที่เพิ่มความเสี่ยงต่อการสแปมข้อมูลลงในบล๊อกเชนของบิตคอยน์&#x20;

## อ้างอิง

\[1] [https://developer.bitcoin.org/reference/transactions.html](https://developer.bitcoin.org/reference/transactions.html)

\[2] [https://en.bitcoin.it/wiki/OP\_RETURN](https://en.bitcoin.it/wiki/OP_RETURN)

\[3] [https://bitcoin.org/en/release/v0.9.0#opreturn-and-data-in-the-block-chain](https://bitcoin.org/en/release/v0.9.0#opreturn-and-data-in-the-block-chain)

\[4] [https://bitcoin.org/en/release/v0.11.0](https://bitcoin.org/en/release/v0.11.0)

\[5] [https://github.com/bitcoin/bitcoin/pull/5286](https://github.com/bitcoin/bitcoin/pull/5286)

\[6][https://cryptoslate.com/bitcoin-cores-op\_return-limit-removal-divides-crypto-community/](https://cryptoslate.com/bitcoin-cores-op_return-limit-removal-divides-crypto-community/)

\[7] [https://arxiv.org/abs/1702.01024](https://arxiv.org/abs/1702.01024)

\[8] [https://exonum.com/doc/version/latest/advanced/bitcoin-anchoring/](https://exonum.com/doc/version/latest/advanced/bitcoin-anchoring/)
