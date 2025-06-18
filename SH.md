Elbette! Aşağıda, tüm adımları bir araya getiren **interaktif bir betik** ve bunu açıklayan profesyonel bir `README.md` içeriği bulacaksın.

---

## 📄 `README.md` İçeriği (Tam Metin)

````markdown
# 🛠️ Wi-Fi Enterprise AP (hostapd + dnsmasq + FreeRADIUS) Başlatma Aracı

Bu sistem, bir Linux cihazını Wi-Fi Access Point (AP) olarak yapılandırır ve 802.1X (Enterprise WPA2) kimlik doğrulama desteği sunar. Yapılandırma üç ana bileşene dayanır:

- **hostapd** → Wi-Fi Access Point
- **dnsmasq** → DHCP sunucusu
- **FreeRADIUS** → EAP üzerinden kullanıcı kimlik doğrulama

---

## 📁 Kullanılan Yapılandırma Dosyaları

| Yol                               | Açıklama                                 |
|----------------------------------|-------------------------------------------|
| `/etc/hostapd/hostapd.conf`      | Kablosuz ağ (SSID, şifreleme, arayüz vb.) |
| `/etc/dnsmasq.conf`              | DHCP ayarları                             |
| `/etc/freeradius/3.0/users`      | Kullanıcı tanımları                       |
| `/etc/freeradius/3.0/clients.conf` | AP’nin RADIUS istemcisi olarak tanımı    |
| `/etc/freeradius/3.0/mods-enabled/eap` | EAP protokol ayarları (PEAP/TLS vs.)    |

---

## 📦 Betikler

Proje iki temel `.sh` betiği içerir. Kullanıcı bu betikleri kullanarak tüm sistemi kolaylıkla başlatabilir veya sıfırlayabilir.

### 🔄 `reset_env.sh` – Ortamı temizle

Bu betik tüm servisleri durdurur, Wi-Fi arayüzünü sıfırlar ve eski kalıntıları temizler.

```bash
#!/bin/bash

echo "[🧹] Ortam sıfırlanıyor..."

sudo systemctl stop wpa_supplicant
sudo systemctl stop NetworkManager
sudo systemctl stop hostapd
sudo systemctl stop dnsmasq
sudo systemctl stop freeradius

sudo killall wpa_supplicant 2>/dev/null

sudo ip link set wlan0 down
sudo ip addr flush dev wlan0
sudo ip link set wlan0 up

sudo rm -f /var/run/hostapd/wlan0

echo "[✓] Ortam başarıyla sıfırlandı."
````

---

### 🚀 `start_ap_radius.sh` – Sistemi başlat

Bu betik tüm gerekli servisleri başlatır.

```bash
#!/bin/bash

echo "[🚀] Servisler başlatılıyor..."

sudo hostapd /etc/hostapd/hostapd.conf &
sleep 2

sudo dnsmasq -C /etc/dnsmasq.conf
sudo systemctl start freeradius

echo "[✓] Tüm servisler başlatıldı. AP yayında, RADIUS dinlemede."
```

---

## 🧭 Tümleşik Ana Betik: `run_manager.sh`

Bu betik, yukarıdaki işlemleri tek bir menü üzerinden yönetmenizi sağlar.

```bash
#!/bin/bash

while true; do
    clear
    echo "==============================="
    echo " Wi-Fi Enterprise AP Manager"
    echo "==============================="
    echo "1) Ortamı sıfırla (reset_env.sh)"
    echo "2) Access Point + Radius başlat (start_ap_radius.sh)"
    echo "3) Servis durumlarını kontrol et"
    echo "4) Çıkış"
    echo -n "> Seçiminiz: "
    read secim

    case $secim in
        1)
            ./reset_env.sh
            read -p "Devam etmek için [Enter] tuşlayın..."
            ;;
        2)
            ./start_ap_radius.sh
            read -p "Devam etmek için [Enter] tuşlayın..."
            ;;
        3)
            echo -e "\n[hostapd]"
            systemctl status hostapd | grep Active
            echo -e "\n[dnsmasq]"
            systemctl status dnsmasq | grep Active
            echo -e "\n[freeradius]"
            systemctl status freeradius | grep Active
            read -p "Devam etmek için [Enter] tuşlayın..."
            ;;
        4)
            echo "[💤] Çıkılıyor..."
            exit 0
            ;;
        *)
            echo "[!] Geçersiz seçenek!"
            sleep 1
            ;;
    esac
done
```

---

## 🚀 Kurulum ve Kullanım

1. Betikleri aşağıdaki gibi yerleştir:

```
/home/user/
├── reset_env.sh
├── start_ap_radius.sh
└── run_manager.sh
```

2. Çalıştırma izinlerini ver:

```bash
chmod +x *.sh
```

3. Ana menüyü başlat:

```bash
./run_manager.sh
```

---

## ✅ Notlar

* `hostapd` arka planda değil, foreground modda çalışır. Terminali açık bırakmanız gerekir.
* Wi-Fi sürücüleri AP modunu desteklemeli (örneğin: `nl80211`).
* RADIUS üzerinden PEAP-MSCHAPv2 kullanılıyorsa `mods-enabled/eap` doğru yapılandırılmalı.

```

---

İstersen `.sh` dosyalarını ayrı ayrı da metin olarak çıkarabilirim ya da `.zip` hâlinde bir arşivlemen için içerik formatı sunabilirim. Hangisini tercih edersin?
```
