# PORTOFOLIO
# PROYEK AKHIR SISTEM OPERASI

## Sistem Antrean Pekerjaan dan Monitoring Proses Linux

---

## 1. Identitas Mahasiswa

| Keterangan | Identitas |
|---|---|
| Nama | Muchammad Andika Rasyid Danendra |
| NIM | 2510512016 |
| Program Studi | Sistem Informasi |
| Mata Kuliah | Sistem Operasi |
| Kelompok | 4 |
| Pilihan Proyek | 5B |
| Posisi | Anggota 5 |
| Tanggung Jawab | Monitoring, Pembatasan Akses, Laporan, dan Docker |

## 2. Deskripsi Proyek

Proyek akhir Sistem Operasi ini berjudul **Sistem Antrean Pekerjaan dan Monitoring Proses Linux**.

Proyek ini bertujuan membangun sistem pengelolaan pekerjaan komputasi sederhana menggunakan Bash Script pada sistem operasi Linux.

Sistem menggunakan metode FIFO (*First In First Out*), yaitu pekerjaan yang masuk lebih dahulu akan diproses lebih dahulu oleh worker.

Fitur utama sistem meliputi:

- Pengajuan pekerjaan dengan parameter yang tervalidasi.
- Pengelolaan antrean pekerjaan menggunakan metode FIFO.
- Penguncian worker agar tidak terjadi pemrosesan ganda.
- Pencatatan status pekerjaan, PID, dan exit status.
- Pembatasan waktu eksekusi pekerjaan (*timeout*).
- Penghentian proses menggunakan SIGTERM dan SIGKILL.
- Monitoring penggunaan CPU dan memori.
- Pembatasan akses menggunakan permission dan group Linux.
- Pembuatan laporan hasil pekerjaan.
- Pengujian akses laporan menggunakan Docker dengan bind mount read-only.

Proyek dikerjakan secara berkelompok dengan pembagian tanggung jawab masing-masing anggota menggunakan Git dan GitHub.

## 3. Repository dan Pengembangan

### Repository utama kelompok

https://github.com/h44dziq/job-queue-kelompok-4

### Repository Fork pribadi

[Masukkan URL repository Fork pribadi setelah berhasil melakukan Fork]

Repository Fork digunakan sebagai tempat dokumentasi dan portofolio pribadi yang tetap terhubung dengan repository utama kelompok.

### Branch yang digunakan

Branch yang digunakan dalam pengembangan bagian monitoring:

`feat/andikarasyid-monitoring`

Branch tersebut digunakan untuk pengembangan dan dokumentasi pekerjaan pada bagian monitoring, akses, serta laporan.

Catatan: sesuaikan nama branch dengan branch yang benar-benar digunakan pada repository Fork pribadi.

## 4. Tanggung Jawab dan Implementasi

Sebagai Anggota 5, saya bertanggung jawab pada bagian monitoring, pembatasan akses, pembuatan laporan, dan integrasi pengujian Docker.

### A. Monitoring CPU dan Memori

File utama:

`scripts/monitor.sh`

Implementasi pada bagian monitoring bertujuan untuk mengamati penggunaan sumber daya sistem Linux, khususnya CPU dan memori.

Fungsi monitoring meliputi:

1. Menampilkan informasi penggunaan CPU.
2. Memantau penggunaan memori sistem.
3. Membantu mengamati kondisi sumber daya ketika sistem antrean pekerjaan berjalan.
4. Menyediakan informasi kondisi sistem untuk keperluan monitoring.

### B. Pembatasan Akses Pengguna

Pembatasan akses dilakukan menggunakan mekanisme user, group, dan permission pada Linux.

Sistem menggunakan beberapa akun dengan hak akses berbeda, yaitu:

| Akun | Peran | Hak akses |
|---|---|---|
| pengguna1 | Pengguna sistem | Mengajukan pekerjaan |
| operator1 | Operator sistem | Menjalankan worker dan membuat laporan |
| tamu1 | Pengguna tamu | Tidak memiliki izin mengajukan pekerjaan |

Pengujian dilakukan untuk memastikan bahwa setiap akun hanya dapat menjalankan aktivitas sesuai hak aksesnya.

### C. Pembuatan Laporan

File utama:

`scripts/laporan.sh`

Script ini digunakan untuk menghasilkan laporan dari sistem antrean pekerjaan.

Informasi yang ditampilkan dalam laporan meliputi:

1. Waktu pembuatan laporan dan nama operator.
2. Jumlah pekerjaan yang menunggu.
3. Jumlah pekerjaan yang sedang berjalan.
4. Jumlah pekerjaan yang berhasil.
5. Jumlah pekerjaan yang gagal.
6. ID pekerjaan, status, PID, exit status, dan nama pengaju.

File laporan dihasilkan pada lokasi:

`/var/lib/job-queue/output/laporan.txt`

Laporan digunakan untuk membantu operator mengetahui hasil pemrosesan pekerjaan pada sistem antrean.

### D. Aktivitas Docker

Docker digunakan untuk melakukan pengujian akses terhadap file laporan yang tersimpan pada host Ubuntu Server.

Konfigurasi pengujian:

| Keterangan | Nilai |
|---|---|
| Image dan tag | alpine:3.20 |
| File laporan | /var/lib/job-queue/output/laporan.txt |
| Direktori host | /var/lib/job-queue/output |
| Direktori container | /data |
| Jenis mount | Bind mount read-only |

Pengujian Docker dilakukan dalam dua kondisi:

1. **Pengujian positif:** memastikan container berhasil membaca file laporan dari host.
2. **Pengujian negatif:** memastikan container tidak dapat mengubah file laporan karena direktori dipasang menggunakan opsi `readonly`.

Selain itu, dilakukan pemeriksaan checksum menggunakan SHA-256 sebelum dan sesudah pengujian untuk memastikan integritas file tetap terjaga.

## 5. Pengujian Fitur Akses, Laporan, dan Docker

Pengujian dilakukan untuk memastikan pembatasan akses berjalan sesuai hak pengguna, laporan berhasil dibuat, dan Docker hanya dapat membaca file laporan melalui bind mount read-only.

### A. Pengujian Pembatasan Akses

**Tujuan:**

Memastikan akun yang tidak memiliki izin tidak dapat mengajukan pekerjaan ke dalam sistem antrean.

Perintah pengujian:

```bash
sudo -u tamu1 /opt/job-queue/scripts/submit.sh job_ringan 1
```

Hasil:

```text
Ditolak: akses ditolak, tamu1 tidak berhak mengajukan job
exit=5
```

Selanjutnya, pengujian dilakukan menggunakan akun yang memiliki hak akses:

```bash
sudo -u pengguna1 /opt/job-queue/scripts/submit.sh job_ringan 1
```

Hasil:

```text
Job diterima: [ID_JOB] (parameter 1)
exit=0
```

**Kesimpulan:** Sistem berhasil membedakan pengguna berdasarkan hak aksesnya. Akun tamu1 ditolak, sedangkan akun pengguna1 berhasil mengajukan pekerjaan.

### B. Pengujian Pembuatan Laporan

**Tujuan:**

Memastikan operator dapat menjalankan pekerjaan dan menghasilkan laporan antrean.

Menjalankan worker:

```bash
sudo -u operator1 /opt/job-queue/scripts/worker.sh
```

Hasil:

```text
Menjalankan [ID_JOB]
Antrean kosong, worker berhenti.
exit=0
```

Membuat laporan:

```bash
sudo -u operator1 /opt/job-queue/scripts/laporan.sh
```

Hasil:

```text
Laporan dibuat: /var/lib/job-queue/output/laporan.txt
exit=0
```

Memeriksa isi laporan:

```bash
cat /var/lib/job-queue/output/laporan.txt
```

Laporan menampilkan jumlah pekerjaan berdasarkan status serta rincian pekerjaan yang telah diproses.

**Kesimpulan:** Operator berhasil menjalankan worker dan menghasilkan laporan yang berisi rekap pekerjaan pada sistem antrean.

### C. Pengujian Positif Docker — Membaca Laporan

**Tujuan:**

Memastikan container Docker dapat membaca file laporan yang tersimpan pada host Ubuntu Server.

Perintah pengujian:

```bash
docker run --rm --mount type=bind,src=/var/lib/job-queue/output,dst=/data,readonly alpine:3.20 cat /data/laporan.txt
```

Hasil:

Container berhasil menampilkan isi `laporan.txt` melalui terminal.

**Kesimpulan:** Pengujian positif berhasil karena container dapat membaca file laporan dari host melalui bind mount read-only.

### D. Pengujian Negatif Docker — Membatasi Penulisan Laporan

**Tujuan:**

Memastikan container Docker tidak dapat mengubah file laporan pada host.

Pemeriksaan checksum awal:

```bash
sha256sum /var/lib/job-queue/output/laporan.txt
```

Percobaan mengubah file melalui container:

```bash
docker run --rm --mount type=bind,src=/var/lib/job-queue/output,dst=/data,readonly alpine:3.20 sh -c 'echo "uji perubahan" >> /data/laporan.txt'; echo "exit=$?"
```

Hasil:

```text
sh: can't create /data/laporan.txt: Read-only file system
exit=1
```

Pemeriksaan checksum setelah pengujian:

```bash
sha256sum /var/lib/job-queue/output/laporan.txt
```

Hasil pengujian menunjukkan bahwa nilai checksum sebelum dan sesudah percobaan penulisan tetap sama.

**Kesimpulan:** Pengujian negatif berhasil karena container tidak dapat melakukan penulisan melalui mount read-only dan integritas file laporan tetap terjaga.

## 6. Ringkasan Hasil Pengujian

| No. | Skenario | Hasil | Exit Status |
|---|---|---|---|
| 1 | Akun tamu1 mengajukan job | DITOLAK | 5 |
| 2 | Akun pengguna1 mengajukan job | DITERIMA | 0 |
| 3 | Operator menjalankan worker | BERHASIL | 0 |
| 4 | Operator membuat laporan | BERHASIL | 0 |
| 5 | Docker membaca laporan | BERHASIL | 0 |
| 6 | Docker mencoba mengubah laporan | DITOLAK | 1 |
| 7 | Pemeriksaan checksum sebelum dan sesudah | SAMA | 0 |

Berdasarkan pengujian tersebut, pembatasan akses, pembuatan laporan, dan aktivitas Docker telah diuji sesuai dengan tujuan masing-masing.

## 7. Analisis Teori Sistem Operasi

### A. User dan Group Linux

Linux menyediakan mekanisme pengelolaan pengguna dan grup untuk mengatur hak akses terhadap file, direktori, serta program.

Dalam proyek ini, akun pengguna1, operator1, dan tamu1 memiliki hak akses yang berbeda sesuai perannya.

### B. Permission Linux

Permission merupakan mekanisme untuk mengatur hak membaca, menulis, dan mengeksekusi file atau direktori.

Pengaturan permission digunakan agar pengguna hanya dapat melakukan aktivitas sesuai dengan hak akses yang diberikan.

### C. Monitoring CPU dan Memori

Monitoring CPU dan memori digunakan untuk mengamati penggunaan sumber daya sistem selama proses pekerjaan berjalan.

Informasi tersebut membantu mengetahui kondisi sumber daya komputer ketika sistem antrean digunakan.

### D. Laporan Sistem

Laporan merupakan hasil rekap aktivitas sistem antrean yang berisi status pekerjaan dan informasi proses.

Laporan membantu operator mengetahui pekerjaan yang telah selesai, sedang berjalan, menunggu, maupun gagal.

### E. Docker Image dan Container

Image merupakan template yang digunakan untuk membuat container. Container merupakan lingkungan terisolasi yang menjalankan perintah berdasarkan image tersebut.

Dalam proyek ini digunakan image Alpine versi 3.20 untuk melakukan pengujian pembacaan laporan.

### F. Bind Mount Read-Only

Bind mount merupakan mekanisme untuk menghubungkan direktori pada host dengan direktori di dalam container.

Opsi `readonly` digunakan agar container hanya dapat membaca isi direktori yang dipetakan dan tidak dapat melakukan penulisan melalui mount tersebut.

### G. SHA-256

SHA-256 merupakan fungsi hash yang menghasilkan nilai unik berdasarkan isi file.

Dalam proyek ini, SHA-256 digunakan untuk membandingkan kondisi file laporan sebelum dan sesudah pengujian Docker negatif. Nilai checksum yang sama menunjukkan bahwa isi file tidak berubah akibat percobaan penulisan tersebut.

## 8. Aktivitas Pengerjaan Proyek

Selain mengerjakan bagian utama, saya juga melakukan beberapa aktivitas selama proses pengembangan proyek.

### A. Persiapan Lingkungan Linux

1. Menggunakan Ubuntu Server 22.04 LTS melalui VirtualBox.
2. Mengakses Ubuntu Server melalui terminal SSH.
3. Memeriksa struktur direktori proyek.
4. Memeriksa script dan konfigurasi sistem antrean.
5. Memeriksa hak akses pengguna dan direktori.
6. Menggunakan perintah Linux untuk memeriksa kondisi sistem.

### B. Pengelolaan Git dan GitHub

Aktivitas Git dan GitHub yang dilakukan meliputi:

1. Melakukan clone repository utama kelompok.
2. Memeriksa struktur file proyek.
3. Mengambil pembaruan repository menggunakan `git fetch`.
4. Melakukan perpindahan branch sesuai kebutuhan pengerjaan.
5. Menggunakan branch pengembangan untuk bagian monitoring.
6. Memeriksa perubahan file dan riwayat commit.
7. Melakukan sinkronisasi dengan repository utama.
8. Membuat Fork repository kelompok untuk dokumentasi portofolio pribadi.

### C. Pengujian dan Dokumentasi

1. Menguji pembatasan akses akun tamu1.
2. Menguji pengajuan pekerjaan menggunakan akun pengguna1.
3. Menjalankan worker menggunakan akun operator1.
4. Menghasilkan laporan antrean pekerjaan.
5. Menguji pembacaan laporan melalui Docker.
6. Menguji penolakan penulisan melalui Docker.
7. Memeriksa checksum sebelum dan sesudah pengujian.
8. Mengumpulkan screenshot sebagai bukti hasil pengujian.

## 9. Bukti Dokumentasi

Bukti yang dilampirkan pada repository portofolio pribadi:

| No. | Bukti | Keterangan |
|---|---|---|
| 1 | Screenshot struktur proyek Ubuntu Server | Lingkungan pengerjaan |
| 2 | Screenshot monitoring CPU dan memori | Implementasi monitoring |
| 3 | Screenshot pembatasan akses tamu1 | Pengujian hak akses |
| 4 | Screenshot pengguna1 berhasil mengajukan job | Pengujian pengajuan pekerjaan |
| 5 | Screenshot operator menjalankan worker | Pengujian pemrosesan job |
| 6 | Screenshot isi laporan.txt | Hasil pembuatan laporan |
| 7 | Screenshot Docker positif | Container berhasil membaca laporan |
| 8 | Screenshot Docker negatif | Penulisan ditolak oleh read-only mount |
| 9 | Screenshot checksum sebelum dan sesudah | Pemeriksaan integritas laporan |
| 10 | Screenshot repository Fork | Dokumentasi portofolio pribadi |

## 10. Kesimpulan dan Pembelajaran

Melalui proyek akhir Sistem Operasi ini, saya memperoleh pengalaman dalam menerapkan pengelolaan pengguna, monitoring sumber daya, pembuatan laporan, dan penggunaan Docker pada lingkungan Linux.

Fokus utama pekerjaan saya sebagai Anggota 5 adalah monitoring CPU dan memori, pembatasan akses pengguna, pembuatan laporan antrean, serta pengujian akses laporan menggunakan Docker.

Saya memahami bagaimana Linux mengatur hak akses melalui user, group, dan permission. Selain itu, saya juga mempelajari cara memantau sumber daya sistem serta menghasilkan laporan untuk mengetahui kondisi dan hasil pekerjaan yang diproses oleh antrean.

Pada bagian Docker, saya mempelajari penggunaan image dan container, pemetaan direktori menggunakan bind mount, pembatasan akses dengan opsi read-only, serta pemeriksaan integritas file menggunakan SHA-256.

Selain aspek teknis, proyek ini juga memberikan pengalaman dalam bekerja secara kolaboratif menggunakan Git dan GitHub, mengelola repository, serta menyusun dokumentasi hasil pengerjaan.

Proyek ini membantu saya memahami penerapan konsep Sistem Operasi secara langsung, khususnya manajemen pengguna, permission, monitoring sumber daya, pengelolaan proses, dan keamanan akses file.

---

**Dokumentasi ini disusun sebagai portofolio kontribusi pribadi dalam Proyek Akhir Sistem Operasi Kelompok 4.**
