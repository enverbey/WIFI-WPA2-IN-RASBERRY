# Enterprise Wi-Fi Access Point with RADIUS on Linux (hostapd + dnsmasq)

Bu proje, bir Linux cihazını (örneğin Raspberry Pi, Ubuntu PC) **Enterprise Wi-Fi Erişim Noktası (WPA2-Enterprise)** haline getirmek için gereken adımları ve konfigürasyon dosyalarını içerir. RADIUS tabanlı kimlik doğrulama (EAP-PEAP) ile çalışan bir yapılandırmadır.

## 📦 Gereksinimler

- Ubuntu / Debian tabanlı Linux sistem
- Wi-Fi destekleyen bir cihaz (`wlan0` arayüzü)
- Aşağıdaki yazılımlar:
  - `hostapd`
  - `freeradius` (veya radiusd)
  - `dnsmasq`
  - `iptables` (isteğe bağlı, NAT yönlendirmesi için)

## 🧱 Proje Yapısı

```bash
.
├── etc/
│   ├── dnsmasq.conf
│   ├── hostapd/
│   │   └── hostapd.conf
│   └── freeradius/
│       └── users
├── start_ap.sh
└── README.md
````

## 🔧 1. Ağ Arayüzü Sabit IP Ayarı

```bash
sudo ip addr add 192.168.50.1/24 dev wlan0
```

## 🛠️ 2. dnsmasq Ayarları

`/etc/dnsmasq.conf` dosyasına aşağıdaki yapı eklenmelidir:

```
interface=wlan0
dhcp-range=192.168.50.10,192.168.50.100,24h
domain-needed
bogus-priv
server=8.8.8.8
server=8.8.4.4
```

Ardından başlatmak için:

```bash
sudo dnsmasq -C /etc/dnsmasq.conf
```

## 📡 3. hostapd Ayarları

`/etc/hostapd/hostapd.conf` içeriği:

```
interface=wlan0
driver=nl80211
ssid=ENTERPRISE_WIFI2
hw_mode=g
channel=6
country_code=TR

ieee8021x=1
eap_server=0
auth_server_addr=127.0.0.1
auth_server_port=1812
auth_server_shared_secret=testing123

wpa=2
wpa_key_mgmt=WPA-EAP
rsn_pairwise=CCMP

eapol_version=2
auth_algs=1
ignore_broadcast_ssid=0

logger_syslog=-1
logger_syslog_level=3

ctrl_interface=/var/run/hostapd
ctrl_interface_group=0

ieee80211n=1
wmm_enabled=1
disassoc_low_ack=0
max_num_sta=20
```

Başlatmak için:

```bash
sudo hostapd /etc/hostapd/hostapd.conf
```

## 🔐 4. FreeRADIUS Kullanıcıları (isteğe bağlı)

`/etc/freeradius/3.0/users` Kullanıcı eklemek dosya sonuna yazın:

```
testuser Cleartext-Password := "testpassword"

```

FreeRADIUS başlatmak için:

```bash
sudo freeradius -X
```

## 🔁 5. Servisleri Durdur/Temizle (gerekli durumlarda)

```bash
sudo systemctl stop NetworkManager
sudo systemctl stop wpa_supplicant
```

## 🚀 6. Hepsini Tek Komutta Çalıştırmak için (İsteğe Bağlı)

Bir `start_ap.sh` betiği ile tüm işlemleri sırayla otomatikleştirebilirsin.

```bash
#!/bin/bash

sudo systemctl stop NetworkManager
sudo systemctl stop wpa_supplicant
sudo ip addr add 192.168.50.1/24 dev wlan0
sudo dnsmasq -C /etc/dnsmasq.conf &
sudo hostapd /etc/hostapd/hostapd.conf
```

## 📡 Bağlantı Testi

İstemci (örneğin bir bilgisayar) bağlandıktan sonra aşağıdaki gibi bir IP almalıdır:

* IP: `192.168.50.x`
* Gateway: `192.168.50.1`
* DNS: `8.8.8.8` veya dnsmasq üzerinden gelen

`ping 8.8.8.8` komutu çalışıyorsa, bağlantı başarılıdır.

---

## 📂 Dosya Listesi (Senin Tarafından Eklenecek)

| Dosya Yolu                 | Açıklama                      |
| -------------------------- | ----------------------------- |
| `etc/dnsmasq.conf`         | DHCP sunucu ayarları          |
| `etc/hostapd/hostapd.conf` | Wi-Fi erişim noktası ayarları |
| `etc/freeradius/users`     | RADIUS kullanıcı ayarları     |
| `start_ap.sh`              | Başlatma betiği (opsiyonel)   |

---

## 📚 Kaynaklar

* [https://wiki.archlinux.org/title/Software\_access\_point](https://wiki.archlinux.org/title/Software_access_point)
* [https://wiki.debian.org/hostapd](https://wiki.debian.org/hostapd)
* [https://wiki.freeradius.org/](https://wiki.freeradius.org/)

---

## 👤 Yazar

Enver Yılmaz – [github.com/enveryilmaz](https://github.com/enveryilmaz)

---

## 📝 Lisans

MIT License – Bu repoyu dilediğiniz gibi kullanabilirsiniz. Referans gösterirseniz sevinirim. 

```

---

### ✅ Sıradaki adım:

Bana aşağıdaki dosyaların içeriklerini sırayla ilet:

1. `/etc/dnsmasq.conf`
2. `/etc/hostapd/hostapd.conf`
3. (Varsa) `/etc/freeradius/users`
4. (Varsa) `start_ap.sh`

Ben de sana her biri için ilgili satırı düzenlenmiş hâlde gönderirim.  
İstersen bu adımları tek tek de yapabiliriz. Hangisini iletmek istersin ilk olarak?
```
