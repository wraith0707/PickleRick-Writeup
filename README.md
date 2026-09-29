# PickleRick - Write-up

* **Platform:** TryHackMe[cite: 3]
* **Zorluk Seviyesi:** Easy
* **Makale Amacı:** Rick and Morty temalı bu makinede keşif, web zafiyet analizi, reverse shell alımı ve yetki yükseltme adımları gerçekleştirilmiştir.

---

## 1. Keşif (Reconnaissance)

Hedef IP adresine yönelik gerçekleştirilen Nmap taraması ile açık portlar ve servisler tespit edilmiştir[cite: 3].

* **Komut:** `nmap -sC -sV -T4 10.114.180.214`[cite: 3]
* **Açık Portlar:**
  * **Port 22 (SSH):** OpenSSH 8.2p1[cite: 3]
  * **Port 80 (HTTP):** Apache httpd 2.4.41[cite: 3]

![Nmap Taraması](images/1.png)[cite: 3]

---

## 2. Servis Analizi & Web Keşif (Enumeration)

* Tarayıcı üzerinden web sayfasına giriş yapıldığında Rick and Morty temalı bir "Help Morty!" arayüzü ile karşılaşılmıştır[cite: 4]. Sayfa kaynağı incelendiğinde yorum satırında bir kullanıcı adı (`R1ckRul3s`) bulunmuştur[cite: 5].
* Yapılan dizin taraması (`gobuster`) sonucunda `login.php`, `portal.php` ve `robots.txt` dosyaları tespit edilmiştir[cite: 6].
* `robots.txt` dosyası ziyaret edildiğinde ilk gizli kelime elde edilmiştir (`Wubbalubbadubdub`)[cite: 7].

| Web Keşif Görselleri | Açıklama |
| :--- | :--- |
| ![Ana Sayfa](<img width="1700" height="671" alt="1" src="https://github.com/user-attachments/assets/3d00ef6a-992c-491d-82d6-ce4ef8a523a4" />
)[cite: 4] | Web arayüzü |
| ![Kaynak Kodu](images/3.png)[cite: 5] | Gizli kullanıcı adı (`R1ckRul3s`) |
| ![Gobuster](images/4.png)[cite: 6] | Dizin tarama sonuçları |
| ![Robots.txt](images/5.png)[cite: 7] | `robots.txt` içeriği |

---

## 3. Sömürü (Exploitation / Foothold)

* `robots.txt`'den elde edilen şifre ile `login.php` üzerinden portala giriş yapılmıştır[cite: 6, 8].
* Portal içerisindeki komut paneli (`Command Panel`) kısmına PHP reverse shell payload'ı yazılarak sistemden bağlantı istenmiştir[cite: 9].
* Saldırgan makinede Netcat ile port dinlemeye alınmış, shell tetiklendikten sonra `python3` ile TTY stabilize edilerek `www-data` yetkisiyle ilk erişim sağlanmıştır[cite: 10].

| Sömürü Adımları | Açıklama |
| :--- | :--- |
| ![Login](images/6.png)[cite: 8] | Portal giriş ekranı |
| ![Command Panel](images/7.png)[cite: 9] | Komut çalıştırma paneli |
| ![Reverse Shell](images/8.png)[cite: 10] | Netcat ile shell alınması |

---

## 4. Yetki Yükseltme & Flag Tespiti (Privilege Escalation)

* Sistemde dizinler gezinilerek gizli içerikler okunmuş ve ilk malzemeler (`mr. meeseek hair`, `1 jerry tear`) toplanmıştır[cite: 11, 12, 13].
* `sudo -l` komutu ile yetkiler kontrol edildiğinde, `www-data` kullanıcısının herhangi bir şifre istemeden (`NOPASSWD: ALL`) tam yetkiye sahip olduğu görülmüştür.
* `sudo su` komutu çalıştırılarak doğrudan **root** yetkisine ulaşılmıştır[cite: 14].

| Yetki Yükseltme ve Flag Adımları | Açıklama |
| :--- | :--- |
| ![Flag 1](images/9.png)[cite: 11] | İlk malzeme dosyası |
| ![Home Dizini](images/10.png)[cite: 12] | `/home/rick` dizin içeriği |
| ![Flag 2](images/11.png)[cite: 13] | İkinci malzeme (`1 jerry tear`) |
| ![Root Yetkisi](images/12.png)[cite: 14] | `sudo -l` ve `sudo su` ile root erişimi |
