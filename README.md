# Laporan UTS — Keamanan Jaringan (Kasus 9)

Repositori ini berisi dokumentasi lengkap pengerjaan Ujian Tengah Semester (UTS) mata kuliah **Keamanan Jaringan**, Program Studi Teknik Informatika.

Secara sederhana, proyek ini adalah **simulasi laboratorium keamanan siber** yang dijalankan di dalam lingkungan virtual terisolasi menggunakan **Docker**. Tujuannya adalah untuk memahami bagaimana sebuah sistem atau jaringan komputer bisa diserang, bagaimana cara mendeteksinya, serta bagaimana cara melindunginya—tanpa membahayakan jaringan asli di dunia nyata.

---

## Identitas Mahasiswa & Penentuan Kasus

* **Nama:** [Deo Ary Anggara]
* **NIM:** `24552011216`
* **Kelas:** TIF RP 24E CNS
* **Dosen Pengampu:** Yasti Aisyah Primianjani, S.Kom., M.S.F.
* **Perhitungan Kasus:** 
  * Jumlah digit NIM: `2 + 4 + 5 + 5 + 2 + 0 + 1 + 1 + 2 + 1 + 6 = 29`
  * Sisa bagi (`29 mod 10`): `9` -> **Mengerjakan Kasus 9**

---

## Struktur Folder & Isi Repositori

Bagi pengunjung atau pembaca yang ingin menelusuri isi repositori ini, berikut adalah fungsi dari setiap berkas dan folder yang tersedia:

```text
├── README.md          # Halaman pengantar dan panduan isi repositori (halaman ini)
├── jawaban.md         # Dokumen utama laporan UTS (berisi perintah, output, bukti visual, dan analisis lengkap)
├── Screenshot/        # Folder berisi tangkapan layar (screenshot) asli dari setiap pengujian di terminal
└── Hasil-Pengerjaan/  # Folder berisi file artefak hasil pengujian (file terenkripsi, sertifikat, dan log)
```

### Penjelasan Detail Berkas:
1. **`jawaban.md` (Laporan Utama):** Berkas paling penting yang memuat seluruh langkah pengerjaan dari Tugas 1 hingga Tugas 7, lengkap dengan penjelasan teknis, bukti visual, dan jawaban teori.
2. **`Screenshot/` (Bukti Visual):** Berisi dokumentasi foto hasil eksekusi perintah di terminal sebagai bukti autentik bahwa seluruh simulasi berhasil dijalankan.
3. **`Hasil-Pengerjaan/` (Artefak Hasil Praktikum):** Menyimpan file keluaran yang dihasilkan selama simulasi berlangsung, meliputi:
   * `laporan-k9.txt` & `hasil-k9.txt`: File teks asli dan hasil dekripsi untuk membuktikan keutuhan data.
   * `laporan-k9.enc`: File yang sudah dikunci (dienkripsi) menggunakan algoritma AES-256.
   * `cert-k9.pem`: Sertifikat keamanan digital (*self-signed certificate*).
   * `dns-k9.log`: Catatan lalu lintas server DNS yang digunakan untuk mendeteksi penyelundupan data.
   * *(Catatan keamanan: Kunci privat `key-k9.pem` sengaja tidak diunggah ke repositori publik demi menjaga kerahasiaan kunci).*

---

## Ringkasan 7 Simulasi yang Dikerjakan

Seluruh praktikum dibagi menjadi 7 topik utama keamanan jaringan:

1. **Tugas 1 — Docker, Volume, dan Compose:** Mempelajari cara membuat ruang penyimpanan data yang tidak hilang meski sistem dihapus, serta menghubungkan dua sistem virtual dalam satu jaringan tertutup.
2. **Tugas 2 — Kriptografi:** Mengamankan isi file menggunakan sandi enkripsi (AES-256), mengecek keaslian data dengan sidik jari digital (SHA-256), serta membuat sertifikat identitas server.
3. **Tugas 3 — ARP Spoofing:** Menyimulasikan teknik penyadapan di jaringan lokal dengan memalsukan alamat perangkat (*MAC Address*), lalu mencegahnya menggunakan penguncian alamat statis (*Static ARP*).
4. **Tugas 4 — Analisis Handshake WPA2:** Menguji keamanan jaringan Wi-Fi dengan menganalisis proses jabat tangan (*handshake*) dan menguji kombinasi kata sandi menggunakan *wordlist*.
5. **Tugas 5 — ICMP dengan Scapy:** Membuat dan mengirimkan paket jaringan khusus menggunakan bahasa pemrograman Python untuk mengecek respons koneksi antar-perangkat.
6. **Tugas 6 — SYN Flood & Three-Way Handshake:** Menyimulasikan serangan yang membanjiri server dengan permintaan koneksi palsu hingga antrean penuh, serta mengamati cara kerja koneksi TCP normal.
7. **Tugas 7 — DNS Exfiltration:** Menyimulasikan cara penyelundupan data rahasia (NIM) yang disamarkan menjadi alamat situs web (*subdomain*), lalu mendeteksi dan membongkarnya kembali lewat catatan log server.

---

## Catatan Etika & Keamanan
Seluruh simulasi serangan (*ARP Spoofing*, *SYN Flood*, dan *DNS Exfiltration*) dilakukan **100% di dalam jaringan virtual lokal Docker yang terisolasi (`internal: true`)** semata-mata untuk tujuan pembelajaran akademis. Tidak ada pemindaian maupun serangan yang diarahkan ke perangkat atau jaringan publik di luar lingkungan lab.
