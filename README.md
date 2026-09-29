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
| ![Ana Sayfa](images/2.jpg) | Web arayüzü |
| ![Kaynak Kodu](images/3.png) | Gizli kullanıcı adı (`R1ckRul3s`) |
| ![Gobuster](images/4.png) | Dizin tarama sonuçları |
| ![Robots.txt](images/5.png) | `robots.txt` içeriği |

---

## 3. Sömürü (Exploitation / Foothold)

* `robots.txt`'den elde edilen şifre ile `login.php` üzerinden portala giriş yapılmıştır.
* Portal içerisindeki komut paneli (`Command Panel`) kısmına PHP reverse shell payload'ı yazılarak sistemden bağlantı istenmiştir.
* Saldırgan makinede Netcat ile port dinlemeye alınmış, shell tetiklendikten sonra `python3` ile TTY stabilize edilerek `www-data` yetkisiyle ilk erişim sağlanmıştır.

| Sömürü Adımları | Açıklama |
| :--- | :--- |
| ![Login](images/6.png) | Portal giriş ekranı |
| ![Command Panel](images/7.png) | Komut çalıştırma paneli |
| ![Reverse Shell](images/8.png) | Netcat ile shell alınması |

---

## 4. Yetki Yükseltme & Flag Tespiti (Privilege Escalation)

* Sistemde dizinler gezinilerek gizli içerikler okunmuş ve ilk malzemeler (`mr. meeseek hair`, `1 jerry tear`) toplanmıştır.
* `sudo -l` komutu ile yetkiler kontrol edildiğinde, `www-data` kullanıcısının herhangi bir şifre istemeden (`NOPASSWD: ALL`) tam yetkiye sahip olduğu görülmüştür.
* `sudo su` komutu çalıştırılarak doğrudan **root** yetkisine ulaşılmıştır.

| Yetki Yükseltme ve Flag Adımları | Açıklama |
| :--- | :--- |
| ![Flag 1](images/9.png) | İlk malzeme dosyası |
| ![Home Dizini](images/10.png) | `/home/rick` dizin içeriği |
| ![Flag 2](images/11.png) | İkinci malzeme (`1 jerry tear`) |
| ![Root Yetkisi](images/12.png) | `sudo -l` ve `sudo su` ile root erişimi |
