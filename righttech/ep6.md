---
description: "Right tech พาทัวร์ ! โหนดบิตคอยน์ทำงานยังไง | Ep.6"
---

# Right Tech EP.6

{% embed url="https://www.youtube.com/watch?v=2I5sxoYWG-U" %}

## Bitcoin Node

### Whtat is Bitcoin Node?

Bitcoin Node คือ computer หรือ Hardware ที่ติดตั้ง sowftware bitcoin (bitcoin client) และเชื่อมต่อกับ node อื่นๆเป็น network bitcoin
ในปัจจุบันมี node ทำกหน้าที่ เช่น

- รักษากฎฉันทามติของ
- บันทึกประวัตของธุรกรรมและ Blockchain ทั้งหมด
- ตรวจสอบธุรกรรมและ Blockchain
- กระจายข้อมูลไปยังโหนดอื่นๆ

### Types

ในปัจจุบันมี node 2 ประเภท

1. **Full Nodes** : เก็บข้อมูลทุกอย่างที่อยู่ใน network bitcoin
2. **Pruned Nodes** : เก็บข้อมูลบางส่วนที่จำเป็นสำหรับการทำงานของ node เท่านั้น

> [!NOTE]
> Light node : โหนดที่เชื่อมต่อกับ network bitcoin โดยไม่ต้องดาวน์โหลด blockchain ทั้งหมด แต่จะดาวน์โหลดแค่ “ส่วนหัวของบล็อก” (block headers) เพื่อใช้ตรวจสอบธุรกรรมแทน

### Bitcoin Core 29.0

#### Network

Drop UPnP

- ยกเลิกการรองรับ UPnP และแทนที่ด้วย NAT-PMP/PCP เพื่อความทันสมัยและรองรับ IPv6
- เพิ่มการรองรับ IPv6 Pinholing โดยแทนที่ libnatpmp ด้วยระบบในตัว ซึ่งเปิดใช้งานด้วย -natpmp รองรับทั้ง IPv4 port forwarding และ IPv6 pinholing
- ปรับการกำหนดพอร์ต onion ให้อัตโนมัติ หากตั้งค่า -port ระบบจะตั้งค่าพอร์ต onion เป็น +1 จากค่าที่ระบุ แทนค่าคงที่ (แก้ปัญหา port ชน)

#### Mempool Policy & Mining

##### Orphan Transaction

orphan transactionเช่น A โอน bitcoin ให้ B และ B โอน bitcoin ให้ C แต่โหนดของ X มีธุรกรรม ที่ B โอนให้ C แต่ไม่มีธุรกรรมที่ A โอนให้ B node ของ X ก็จะไม่สามารถบอกได้ว่าธุรกรรมที่ B โอนให้ C นั้นถูกต้องหรือไม่ เนื่องจากไม่สามารถยืนยันได้ว่า bitcoin ที่ A โอนให้ C นั้นมีอยู่จริง สิ่งที่โหน X จะทำคือ อ่าน Transaction ID และจะของธุรกรรมนี้กับ node ที่เชื่อมอยู่ และ Transaction ที่ B โอนให้ C จะถูกเก็บไว้ใน orphan pool และ node X จะขอร้องเรื่อยๆจนกว่าจะได้ ธุรกรรมที่ A โอนให้ B เมื่อได้รับแล้วและตรวจสอบว่าถูกต้อง ค่อยนำธุรกรรมที่อยู่ใน orphan pool มาใส่่ mempool

##### ephemeral dust

สุมมุติว่า A โอนให้ B 100 sat แต่ปัญหาคือ B จะนำ 100 sat ไปใช้ต่อได้ยังไงให้เมื่อค่า fee ก็เกิน 100 sat แล้ว
ถ้า B ต้องการะใช้ UTXO นี้ (dust) B ต้องสร้าง Transaction V3 ซึ่งจะสามารถใส่ UTXO ที่เป็น dust ทั้งหมดได้่เพื่อรวบธุรกรรม เป็น output จ่ายไปปลายทางที่ต้องาการ

##### Block Reserved Weight Bug Fix

ใน Bitcoin block สามารถบรรนจุข้อมูลได้ 4,000,000 weight units (WU)
ซึ่งในเวอร์ชันก่อนหน้า (ก่อน v29.0) มีการ "จองพื้นที่เก็บข้อมูลล่วงหน้าไว้" 8,000 WU
ซึ่งนี่คือค่าสูงสุดที่เป็นไปได้แต่ในคาวมเป็นจริงแล้ว อาจจะน้อยกว่านี้ก็ได้ทำให้ miner ที่ไม่ทราบเรื่องนี้อาจบรรนจุธุรกรรมไม่เต็มพื้นที่

ในเวอร์ชันใหม่ได้เพิ่มตัวเลือกใหม่คือ -blockreservedweight ให้ผู้ใช้ ตั้งค่าการจองเองได้ และยังคงใช้ 8,000 WU เป็นค่า default เพื่อความเข้ากันได้กับเวอร์ชันเดิม

##### RPC

Add new RPC:

- getdescriptoractivity

Updated RPCs:

- testmempoolaccept
- submitblock
- getmininginfo
- getblock, getblockheader, getblockchaininfo, getchainstates
- getblocktemplate 

##### RBF

ยกเลิกตัวเลือก mempoolfullrbf และเปลี่ยนให้ full-RBF เป็นค่ามาตรฐาน

ก่อน v29.0
bitcoind -mempoolfullrbf=1

V29.0

- รองรับ Full-RBF โดยอัตโนมัติ
- และตัวเลือก -mempoolfullrbf จะใช้ไม่ได้ (ถูกลบไปแล้ว)

Ex

Tx A: ส่ง 0.01 BTC → มีค่าธรรมเนียม 5 sat/vB
Tx B: ส่ง 0.01 BTC (input เดิม) → ค่าธรรมเนียม 30 sat/vB

ถ้า Full-RBF ถูกเปิด:

- Mempool จะลบ Tx A และใส่ Tx B แทน
- Miner ก็จะเลือก Tx B เพราะ fee สูงกว่า

> [!NOTE]
> เวอร์ชัน 26.x และก่อนหน้านี้ จะ ไม่ได้รับการอัปเดตอีกต่อไป
> 2 สัปดาห์หลังจากปล่อยเวอร์ชันนี้ จะมีการเปิดเผยช่องโหว่ระดับกลาง-สูงของเวอร์ชัน 26.x และช่องโหว่ระดับต่ำของเวอร์ชัน 28.x ตามนโยบาย security policy

### Overview Workflow Node

1. รับ transaction เขามาตรรวจสอบความถูกต้องและใส่ลงใน mempool
2. miner ดึงไปปิด block
3. miner ส่ง block ไปยัง node ตรวจสอบความถูกต้อง
4. เมื่อ block ถูกต้อง node ก็จะส่งต่อไปยัง node อื่นๆใน network

ในกรณีที่มี node ใหม่เข้ามาใน network node ใหม่จะ copy blick ทั้งหมดจาก node อื่นๆใน network

สามารถดูวิธีกการรัน node ได้ที่ [https://www.yakihonne.com/article/naddr1qvzqqqr4gupzqqm59ss9edkgmpsrrjfmcj5m85vysnpjepjk8lqwyxy3pgkln2jaqqgrjdn9xgmxzdp5vsuxgd35vv6rswyyjc2]
