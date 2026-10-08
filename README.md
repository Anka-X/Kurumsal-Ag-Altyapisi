# 🏢 Kurumsal Ağ Altyapısı Tasarımı ve Ağ Geçidi Güvenlik Simülasyonu

433 m² kurumsal ofis yerleşkesi için sıfırdan tasarlanmış; ağ segmentasyonu, katmanlı IP hiyerarşisi, kurumsal kimlik doğrulama ve merkezi yerel servisleri içeren uçtan uca sanallaştırılmış ağ altyapı projesidir.

Projenin tüm mimari detaylarını, adım adım firewall/sunucu konfigürasyonlarını ve canlı soket/port doğrulama testlerini **proje dosyaları arasındaki PDF raporunda** proje dosyalarını ise **https://mega.nz/folder/0cpCyKBS#qscq6d78lRg9O3dNWCHe1Q** bulabilirsiniz.

---

## 📌 Proje Özeti

* **Fiziksel Yerleşim & Sistem Odası:** 9 ofis, toplantı ve seminer odalarından oluşan 433m² çalışma alanında 1 ofis izole Sistem Odası'na dönüştürüldü. 31 masaüstü bilgisayar ve 1 ağ yazıcısı için kablolama planı kurgulandı.
* **Ağ Segmentasyonu:** Ağ trafiği performans ve güvenlik amacıyla 3 bağımsız katmana bölündü:
  * `WAN`: Dış Hat - Geniş Bant İnternet
  * `LAN`: Kablolu Ofis İstemcileri & Sunucular Havuzu
  * `KABLOSUZ_AG`: İzole Mobil İstemciler ve AP Havuzu
* **Merkezi Ağ Geçidi OPNsense:** Sistem üzerinde Suricata IDS/IPS, Kea DHCPv4, dahili Unbound DNS ve Outbound NAT kuralları devreye alındı. Zero-Trust prensibiyle ağlar arası geçişler kısıtlandı.
* **Kurumsal Kimlik Doğrulama:** Kablosuz ağa bağlanan mobil cihazlar için FreeRADIUS veritabanı entegrasyonlu Captive Portal Karşılama Ekranı perdesi oluşturuldu.
* **Debian 13 İzole Kurumsal Servisler:**
  * **File Server:** Samba Port 445 ile ortak departman klasör yönetimi.
  * **Backup Server:** Rsync over SSH Port 22 ile izole diferansiyel yedekleme hattı.
  * **Print Server:** CUPS Port 631 tabanlı ağ yazdırma kuyruğu entegrasyonu.

---

## 🛠 Kullanılan Teknolojiler

* **Sanallaştırma:** Oracle VM VirtualBox, Intel PRO/1000 MT Emülasyonu
* **Ağ Geçidi / Firewall:** OPNsense 26.1.7_3 FreeBSD tabanlı
* **Ağ Güvenliği & Tehdit Analizi:** Suricata IDS/IPS
* **Kimlik Doğrulama:** FreeRADIUS, OPNsense Captive Portal
* **Sunucu Mimari:** Debian GNU/Linux 13 Trixie
* **Servis Protokolleri:** Samba, Rsync, CUPS, IPP, SSH

---

## 📐 Ağ Tasarım Topolojisi

Projeye ait "Boş Ofis Yerleşim Planı" ve donanımların yerleştirildiği "Ağ Yapılandırma Krokisi" yukarıdaki proje dosyaları arasında JPG formatında yer almaktadır. Genel mimari hiyerarşi şu şekildedir:

```text
[ İnternet / WAN ] 
        │ 10.0.2.15/24
┌───────▼────────────────────────────────────────────────┐
│               OPNsense Ağ Geçidi                       │
│   Kea DHCP, Unbound DNS, Suricata, FreeRADIUS, NAT     │
└───┬────────────────────────────────────────────────┬───┘
    │ LAN em1 - 192.168.1.1/24                       │ WLAN em2 - 10.0.0.1/24
    ├─► Ofis_File_Server .10 / Samba                 └─► Captive Portal Port 8000
    ├─► Ofis_Backup_Server .11 / Rsync                      └─► Doğrulanmış Mobil Cihazlar
    ├─► Ofis_Print_Server .12 / CUPS
    └─► Kablolu Ofis PC'leri DHCP
