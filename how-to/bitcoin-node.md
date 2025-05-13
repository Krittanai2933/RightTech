---
description: 'How To install Bitcoin core'
---

# วิธีติดตั้ง Bitcoin Node + Tor บน Ubuntu Server

Bitcoin Node คือเครื่องคอมพิวเตอร์ที่ติดตั้ง Bitcoin Client ในปัจจุบันมีหลายวิธีมากให้ติดตั้ง แต่ในบความนี้จะมาติดตั้งลงบน Linux 

**อย่าลืมลง OS Linux ก่อนครับ**
> บทความนี้ผมใช้ Ubuntu server

มาเริ่มกันเลย
## 🔧 อัปเดตระบบและติดตั้งเครื่องมือพื้นฐาน
```sh
sudo apt update && sudo apt upgrade -y
```
```sh
sudo apt install wget curl gnupg tar ufw -y
```

## 🔐 ตั้งค่า Firewall เพื่อความปลอดภัย
เปิด Port เท่าที่จำเป็นต้องใช้
```sh
sudo ufw allow 22/tcp comment 'ssh'
sudo ufw allow 9051/tcp comment 'tor'
sudo ufw allow 9050/tcp comment 'tor'
sudo ufw allow 8333/tcp comment 'Bitcoin core peer'
sudo ufw allow 8332/tcp comment 'Bitcoin core RPC'
```
เปิด Firewall
```sh
sudo ufw enable
```
ตรวจสอบ Port Firewall 
```sh
sudo ufw status
```
เครื่องของเราตอนนี้ก็พร้อมแล้วไปติดตั้ง Bitcoin node กันเลย
> ผมใช้ Bitcoin core V29.0 คุณอยากใช้เวอร์ชันไหนเลือกชุดกฎที่คุณต้องการได้เลย 

## ⬇️ ดาวน์โหลดและตรวจสอบ Bitcoin Core
ดาวน์โหลด Bitcoin core ลงเครื่อง
```sh
wget https://bitcoincore.org/bin/bitcoin-core-29.0/bitcoin-29.0-x86_64-linux-gnu.tar.gz
```
ดาวน์โหลด signatures ล่าสุด
```sh
wget https://bitcoincore.org/bin/bitcoin-core-29.0/SHA256SUMS
```
```sh
wget https://bitcoincore.org/bin/bitcoin-core-29.0/SHA256SUMS.asc
```

### นำเข้าคีย์ของผู้พัฒนาและตรวจสอบลายเซ็น
นำเข้าคีย์ของผู้พัฒนา
```sh
curl -s "https://api.github.com/repositories/355107265/contents/builder-keys" | grep download_url | grep -oE "https://[a-zA-Z0-9./-]+" | while read url; do curl -s "$url" | gpg --import; done
```
output
```
gpg: key 17565732E08E5E41: 29 signatures not checked due to missing keys
gpg: /home/admin/.gnupg/trustdb.gpg: trustdb created
gpg: key 17565732E08E5E41: public key "Andrew Chow <andrew@achow101.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
gpg: no ultimately trusted keys found
[...]
```
ตรวจสอบ Signature
```sh
gpg --verify SHA256SUMS.asc SHA256SUMS
```
output
```
gpg: Good signature from...
Primary key fingerprint:...
```
ตรวจสอบซอฟต์แวร์ว่าถูกต้องไหม
```sh
sha256sum --ignore-missing --check SHA256SUMS
```
output
```
bitcoin-29.0-x86_64-linux-gnu.tar.gz: OK
```

## 📦 ติดตั้ง Bitcoin Core
แตกไฟล์ Bitcoin core 
```sh
tar -xzvf bitcoin-29.0-x86_64-linux-gnu.tar.gz
```
ติดตั้ง Bitcoin core
```sh
sudo install -m 0755 -o root -g root -t /usr/local/bin bitcoin-29.0/bin/bitcoin-cli bitcoin-29.0/bin/bitcoind
```
ตรวจสอบเวอร์ชั่น
```sh
bitcoind --version
```
ทดสอบ Bitcoin core
```sh
bitcoind -daemon
```
สั่ง Bitcoin core หยุดทำงาน
```sh
bitcoin-cli stop
```

## 🧹 ลบไฟล์ติดตั้งที่ไม่ใช้แล้ว
```sh
sudo rm -r bitcoin-29.0 bitcoin-29.0-x86_64-linux-gnu.tar.gz SHA256SUMS SHA256SUMS.asc
```

## ⚙️ ตั้งค่า bitcoin.conf
สร้างไฟล์ bitcoin.conf
```sh
nano ~/.bitcoin/bitcoin.conf
```

ตัวอย่างไฟล์ bitcoin.conf
```
# [core]
# Run in the background as a daemon and accept commands.
daemon=1
# Maintain a full transaction index, used by the getrawtransaction rpc call.
txindex=1

# [network]
# Use separate SOCKS5 proxy <ip:port|path> to reach peers via Tor hidden services. May be a local file path prefixed with 'unix:'
onion=127.0.0.1:9050
bind=127.0.0.1
onlynet=onion
listen=1

# [rpc]
# Accept command line and JSON-RPC commands.
server=1
rpcuser=username
rpcpassword=password
rpcbind=0.0.0.0:8332
rpcallowip=0.0.0.0/0
deprecatedrpc=accounts
```
> คุณสามรถตั้งค่า bitcoin.conf ด้วยตัวเองได้ไปที่ 
https://jlopp.github.io/bitcoin-core-config-generator


## 🕵️ ติดตั้งและตั้งค่า Tor สำหรับ Bitcoin RPC
ติดตั้ง tor
```sh
sudo apt install tor -y
```
แก้ไข Tor configuration
```sh
sudo nano /etc/tor/torrc
```
เพิ่มค่า Tor configuration
```sh
ControlPort 9051
CookieAuthentication 1
CookieAuthFileGroupReadable 1
HiddenServiceDir /var/lib/tor/bitcoin/bitcoinrpc
HiddenServiceVersion 3
HiddenServicePort 8332 127.0.0.1:8332
```
สร้าง Directory สำหรับ Hidden Service
```sh
sudo mkdir -p /var/lib/tor/bitcoin/bitcoinrpc
```
เปลี่ยน Ownership และ Permissions ของ Directory
```sh
sudo chown -R debian-tor:debian-tor /var/lib/tor/bitcoin/bitcoinrpc
sudo chmod 700 /var/lib/tor/bitcoin/bitcoinrpc
```
เพิ่ม User  ให้กับ Group debian-tor
```sh
sudo usermod -a -G debian-tor username
```
> อย่าลืมเปลี่ยน USERNAME ให้ตรงกับ user ของคุณ
restart tor
```sh
sudo systemctl restart tor
```

## 🔁 สร้าง systemd service
> การสร้าง Systemd Service เพื่อให้ระบบสามารถเรียกใช้ bitcoin daemon โดยอัตโนมัติในพื้นหลังได้
```sh
sudo nano /etc/systemd/system/bitcoind.service
```
configuration
```
[Unit]
Description=Bitcoin daemon
After=network.target

[Service]
ExecStart=/usr/local/bin/bitcoind -daemon -conf=/home/username/.bitcoin/bitcoin.conf -datadir=/home/username/.bitcoin
ExecStop=/usr/local/bin/bitcoin-cli stop
User=username
Group=username
Type=forking
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
> อย่าลืมเปลี่ยน USERNAME ให้ตรงกับ user ของคุณ

เปิดใช้งาน Bitcoind
```sh
sudo systemctl enable bitcoind
```
```sh
sudo systemctl start bitcoind
```
ตรวจสอบว่า Bitcoind ทำงานไหม
```sh
sudo systemctl status bitcoind
```
output
```
● bitcoind.service - Bitcoin daemon
     Loaded: loaded (/etc/systemd/system/bitcoind.service; enabled; preset: enabled)
     Active: active (running) since Sat 2025-04-19 14:10:16 UTC; 23min ago
    Process: 812 ExecStart=/usr/local/bin/bitcoind -daemon -conf=/home/node/.bitcoin/bitcoin.conf -datadir=/home/node/.bitcoin (code=exited, >
   Main PID: 876 (bitcoind)
      Tasks: 26 (limit: 4552)
     Memory: 3.2G (peak: 3.2G swap: 252.0K swap peak: 252.0K)
        CPU: 12min 55.480s
     CGroup: /system.slice/bitcoind.service
             └─876 /usr/local/bin/bitcoind -daemon -conf=/home/node/.bitcoin/bitcoin.conf -datadir=/home/node/.bitcoin

Apr 19 14:10:14 node systemd[1]: Starting bitcoind.service - Bitcoin daemon...
Apr 19 14:10:16 node bitcoind[812]: Bitcoin Core starting
Apr 19 14:10:16 node systemd[1]: Started bitcoind.service - Bitcoin daemon.
```

## ✅ ตรวจสอบ Bitcoin core หลังติดตั้ง

ตรวจสอบ Bitcoin core เขื่อมต่อกับ Tor network ไหม
```sh
bitcoin-cli -netinfo
```
Output
```
        onion   total   block
in          0       0
out        10      10       2
total      10      10

Local addresses
address.onion     port   8333    score      4


```
ตรวจสอบ Bitcoin core เปิด Port อะไรบ้าง
```sh
sudo ss -tulpn | grep bitcoind
```
Output
```
tcp   LISTEN 0      4096              127.0.0.1:8333      0.0.0.0:*    users:(("bitcoind",pid=876,fd=30))
tcp   LISTEN 0      128                 0.0.0.0:8332      0.0.0.0:*    users:(("bitcoind",pid=876,fd=12))
```
ตรวจสอบ Bitcoin core ซิงก์ข้อมูลไปถึงไหนแล้ว
```sh
echo "$(bitcoin-cli getblockchaininfo | jq -r '.verificationprogress * 100') %"
```
> ต้องติดตั้ง jq ก่อน (sudo apt install jq)
ตรวจสอบ Bitcoin node เชื่อมต่อกับโหนดอื่นอยู่กี่โหหนด
```sh
bitcoin-cli getconnectioncount
```

**เพียงแค่นี้ก็มี Bitcoin node ใช้งานแล้ว**

## 🔑 วิธีเชื่อมต่อ Wallet ผ่าน RPC
ในไฟล์ bitcoin.conf เราได้ตั้ง userame:password ของ RPC ไว้แล้วส่วนนี้แหละที่เราจะเอาไปเชื่อมต่อเข้ากับ Wallet ต่าง ๆ ที่เราใช้งาน

### ผ่าน IP LAN
192.168.x.xxx:8332
user:password
```
ip address เครื่องที่ติตตั้ง bitcoin core
port: 8332
rpcuser=username ของคุณเอง
rpcpassword=password ของคุณเอง
```
> วิธีตรวจสอบ ip เครื่อง พิมพ์ ip a และสั่งเกตคำว่า inet 192.168.x.xxx/24

### ผ่าน Tor Address
ต้องหา tor address ที่เราตั้งค่าไว้ก่อนโดยใช้คำสั่งนี้ 
```sh
sudo cat /var/lib/tor/bitcoin/bitcoinrpc/hostname
```
output
```
address.onion
```
address.onion:8332
user:password
```
address.onion
port: 8332
rpcuser=username ของคุณเอง
rpcpassword=password ของคุณเอง
```

วิธีติดตั้งเองทั้งหมดนั้นทำให้เราสามารถเลือกเวอร์ชันที่จะลงเองได้และปรับแต่งได้ตามความต้องการของเรา

**ขอบคุณที่อ่านมาถึงตรงนี้ 🙏**  
#RightTech