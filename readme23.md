🧱 1. freeradius servisini başlat
- sudo systemctl start freeradius

Hata alırsan status ile kontrol edebilirsin:
- sudo systemctl status freeradius

🔌 2. wpa_supplicant ve NetworkManager servislerini durdur (Wi-Fi arayüzü boş kalsın)
- sudo systemctl stop wpa_supplicant
- sudo killall wpa_supplicant
- sudo systemctl stop NetworkManager

🧹 3. Wi-Fi arayüzünü sıfırla
- sudo ip link set wlan0 down
- sudo ip addr flush dev wlan0
- sudo ip link set wlan0 up


🧽 4. hostapd ile Access Point yayına başlat
İlk önce eski kontrol soketi varsa temizle:
- sudo rm -f /var/run/hostapd/wlan0

Sonra hostapd’yi başlat:
- sudo hostapd /etc/hostapd/hostapd.conf

Bu işlem sırasında terminalde kalır. Şu çıktılar görünecekse her şey yolunda:
- wlan0: interface state UNINITIALIZED->ENABLED
- wlan0: AP-ENABLED

--- 
🔌 1. dnsmasq servisini başlat
- sudo systemctl start dnsmasq
Veya özel konfigürasyon kullandıysan:
- sudo dnsmasq -C /etc/dnsmasq.conf



✅ 2. dnsmasq zaten çalışıyor mu?
sudo systemctl status dnsmasq




biosecure@goldeneye:~ $ sudo systemctl restart hostapd
biosecure@goldeneye:~ $ sudo nano /etc/dnsmasq.conf
biosecure@goldeneye:~ $ sudo nano /etc/dnsmasq.conf
biosecure@goldeneye:~ $ sudo nano /etc/hostapd/hostapd.conf
biosecure@goldeneye:~ $ sudo nano /etc/freeradius/3.0/clients.conf
biosecure@goldeneye:~ $ sudo nano /etc/freeradius/3.0/mods-enabled/eap
biosecure@goldeneye:~ $ sudo nano /etc/freeradius/3.0/mods-config/files/authorize
biosecure@goldeneye:~ $ sudo nano /media/biosecure/rootfs/etc/NetworkManager/NetworkManager.conf
biosecure@goldeneye:~ $ sudo nano /etc/freeradius/3.0/clients.conf