# PickleRick - Write-up

* **Platform:** TryHackMe
* **Zorluk Seviyesi:** Easy
* **Makale Amacı:** Rick and Morty temalı bu makinede keşif, web zafiyet analizi, reverse shell alımı ve yetki yükseltme adımları gerçekleştirilmiştir.

---

## 1. Keşif (Reconnaissance)

Hedef IP adresine yönelik gerçekleştirilen Nmap taraması ile açık portlar ve servisler tespit edilmiştir.

* **Komut:** `nmap -sC -sV -T4 10.114.180.214`
* **Açık Portlar:**
  * **Port 22 (SSH):** OpenSSH 8.2p1
  * **Port 80 (HTTP):** Apache httpd 2.4.41

<img width="1700" height="671" alt="1" src="https://github.com/user-attachments/assets/73d72c04-83bd-4656-a31e-bfb945abd7b1" />

---

## 2. Servis Analizi & Web Keşif (Enumeration)

* Tarayıcı üzerinden web sayfasına giriş yapıldığında Rick and Morty temalı bir "Help Morty!" arayüzü ile karşılaşılmıştır. Sayfa kaynağı incelendiğinde yorum satırında bir kullanıcı adı (`R1ckRul3s`) bulunmuştur.
* Yapılan dizin taraması (`gobuster`) sonucunda `login.php`, `portal.php` ve `robots.txt` dosyaları tespit edilmiştir.
* `robots.txt` dosyası ziyaret edildiğinde ilk gizli kelime elde edilmiştir (`Wubbalubbadubdub`).

| Web Keşif Görselleri | Açıklama |
| :--- | :--- |
| <img width="1227" height="616" alt="2" src="https://github.com/user-attachments/assets/cd6eb2bf-2109-4b43-8b23-5d62716841a0" />
| Web arayüzü |
|<img width="1357" height="606" alt="3" src="https://github.com/user-attachments/assets/0af23e73-8651-4b7f-96bf-a28ad2e92153" />
| Gizli kullanıcı adı (`R1ckRul3s`) |
|<img width="1911" height="786" alt="4" src="https://github.com/user-attachments/assets/4edf68b2-4fba-4170-aed2-d2c62b11ae82" />
| Dizin tarama sonuçları |
|<img width="824" height="234" alt="5" src="https://github.com/user-attachments/assets/deebde2b-b33a-45c6-b0dd-0b9e682435db" />
| `robots.txt` içeriği |

---

## 3. Sömürü (Exploitation / Foothold)

* `robots.txt`'den elde edilen şifre ile `login.php` üzerinden portala giriş yapılmıştır.
* Portal içerisindeki komut paneli (`Command Panel`) kısmına PHP reverse shell payload'ı yazılarak sistemden bağlantı istenmiştir.
* Saldırgan makinede Netcat ile port dinlemeye alınmış, shell tetiklendikten sonra `python3` ile TTY stabilize edilerek `www-data` yetkisiyle ilk erişim sağlanmıştır.

| Sömürü Adımları | Açıklama |
| :--- | :--- |
|<img width="1406" height="665" alt="6" src="https://github.com/user-attachments/assets/7b206a9b-4a98-4b4d-bcc7-d3422b7a9080" />
| Portal giriş ekranı |
|<img width="897" height="281" alt="7" src="https://github.com/user-attachments/assets/c4cc0af6-1c2d-4453-8218-6b01966d01e1" />
| Komut çalıştırma paneli |
|<img width="1292" height="257" alt="8" src="https://github.com/user-attachments/assets/78573b5e-8ed7-404f-b21b-8bd73576292d" />
| Netcat ile shell alınması |

---

## 4. Yetki Yükseltme & Flag Tespiti (Privilege Escalation)

* Sistemde dizinler gezinilerek gizli içerikler okunmuş ve ilk malzemeler (`mr. meeseek hair`, `1 jerry tear`) toplanmıştır.
* `sudo -l` komutu ile yetkiler kontrol edildiğinde, `www-data` kullanıcısının herhangi bir şifre istemeden (`NOPASSWD: ALL`) tam yetkiye sahip olduğu görülmüştür.
* `sudo su` komutu çalıştırılarak doğrudan **root** yetkisine ulaşılmıştır.

| Yetki Yükseltme ve Flag Adımları | Açıklama |
| :--- | :--- |
|<img width="703" height="76" alt="9" src="https://github.com/user-attachments/assets/c228da33-4b4e-4f16-a554-f1cbff96ed3d" />
| İlk malzeme dosyası |
|<img width="840" height="157" alt="10" src="https://github.com/user-attachments/assets/de4c6601-e8bf-4b9a-984c-16effff42366" />
| `/home/rick` dizin içeriği |
|<img width="1136" height="102" alt="11" src="https://github.com/user-attachments/assets/8fd552af-a6ec-4844-8dc5-12f485488968" />
| İkinci malzeme (`1 jerry tear`) |
|<img width="1671" height="376" alt="12" src="https://github.com/user-attachments/assets/64c8e853-a0f2-43f1-b504-d48fe33e171c" />
| `sudo -l` ve `sudo su` ile root erişimi |
