# Standard Operating Procedure (SOP)

## Audit, Penemuan, dan Dokumentasi Infrastruktur Server Baru (Blind Takeover)

| Dokumen ID | SOP-IT-OPS-001             | Versi           | v1.0                 |
| ---------- | -------------------------- | --------------- | -------------------- |
| Departemen | IT Infrastructure / DevOps | Tanggal Efektif | 12 Juli 2026         |
| Status     | Active                     | Penulis         | System Administrator |

---

### 1. TUJUAN

Prosedur ini dibuat untuk memberikan panduan langkah demi langkah bagi _System Administrator_ baru dalam melakukan audit, pemetaan, dan dokumentasi server berbasis **Ubuntu Server** yang belum memiliki dokumentasi formal (_blind takeover_). SOP ini bertujuan untuk mempermudah proses _troubleshooting_ di masa mendatang dan meminimalkan risiko operasional.

### 2. RUANG LINGKUP

SOP ini berlaku untuk semua server fisik (bare-metal), _Virtual Private Server_ (VPS), maupun _cloud instances_ yang berjalan di atas sistem operasi Ubuntu Server yang menjadi tanggung jawab tim infrastruktur baru.

### 3. PRASYARAT

Sebelum menjalankan prosedur ini, pastikan Anda memiliki:

- Akses SSH ke server target.
- Hak akses pengelola (`sudo` atau `root`).
- Repositori Git / GitHub untuk menyimpan dokumentasi hasil audit.

---

### 4. LANGKAH-LANGKAH AUDIT DAN PERINTAH (COMMANDS)

#### LANGKAH 4.1: Audit Informasi Sistem & Perangkat Keras (Hardware)

Langkah awal untuk mengetahui kapasitas, batas kemampuan _resource_, serta versi kernel/OS dasar server.

- **Memeriksa Versi OS Lengkap:**

  ```bash
  lsb_release -a
  ```

- **Memeriksa Versi Kernel & Arsitektur OS:**

```bash
uname -a

```

- **Memeriksa Kapasitas CPU (Jumlah Core & Model):**

```bash
lscpu

```

- **Memeriksa Total RAM dan Penggunaannya:**

```bash
free -h

```

- **Memeriksa Kapasitas Penyimpanan (Storage/Disk) & Partisi:**

```bash
df -h

```

- **Memeriksa Daftar Device Penyimpanan Fisik/Block Storage:**

```bash
lsblk

```

#### LANGKAH 4.2: Audit Jaringan & Keamanan Firewall

Langkah untuk mengidentifikasi bagaimana server berkomunikasi dan mengamankan diri dari luar.

- **Memeriksa IP Address yang Terpasang pada Interface Jaringan:**

```bash
ip a

```

- **Memeriksa Jalur Routing & Default Gateway:**

```bash
ip route

```

- **Memeriksa Status UFW (Uncomplicated Firewall):**

```bash
sudo ufw status verbose

```

- **Memeriksa Aturan Iptables (Jika UFW tidak aktif/tidak digunakan):**

```bash
sudo iptables -L -n -v

```

#### LANGKAH 4.3: Audit Service Aktif & Port Listening

Langkah kritikal untuk memetakan aplikasi apa saja yang sedang berjalan dan menerima koneksi jaringan.

- **Memeriksa Port Terbuka (Listening) dan Proses Pemiliknya:**

```bash
sudo ss -tulpn

```

_(Catatan: Perhatikan kolom `Local Address:Port` dan `Process Staff` untuk mengidentifikasi aplikasi seperti Nginx, PostgreSQL, Redis, dll)._

- **Memeriksa Semua Service yang Sedang Berjalan (Running) di Systemd:**

```bash
systemctl list-units --type=service --state=running

```

- **Memeriksa Service yang Dikonfigurasi Otomatis Berjalan Saat Booting (Enabled):**

```bash
systemctl list-unit-files --type=service --state=enabled

```

#### LANGKAH 4.4: Audit Lingkungan Containerization (Docker)

Langkah untuk mendeteksi apakah aplikasi dijalankan terisolasi di dalam kontainer Docker.

- **Memeriksa Status Service Docker:**

```bash
systemctl status docker

```

- **Memeriksa Container Docker yang Sedang Berjalan (Active):**

```bash
sudo docker ps

```

- **Memeriksa Semua Container Docker (Termasuk yang Berhenti/Eksit):**

```bash
sudo docker ps -a

```

- **Memeriksa Daftar Docker Compose Project yang Aktif:**

```bash
sudo docker compose ls

```

#### LANGKAH 4.5: Audit User, Akses Administratif, & Keamanan

Langkah untuk memastikan tidak ada celah keamanan atau user ilegal yang memiliki akses kontrol tinggi.

- **Memeriksa User yang Sedang Login Saat Ini:**

```bash
w

```

- **Memeriksa Daftar Semua User Terdaftar di Sistem:**

```bash
cat /etc/passwd | cut -d: -f1

```

- **Memeriksa User yang Tergabung dalam Group Sudo (Memiliki Hak Akses Administrator):**

```bash
grep -Po '^sudo.+:\K.*$' /etc/group

```

- **Memeriksa SSH Public Key yang Berhak Akses Tanpa Password:**

```bash
cat ~/.ssh/authorized_keys

```

#### LANGKAH 4.6: Identifikasi Manajemen Log untuk Troubleshooting

Peta lokasi file log utama yang wajib diperiksa jika server mengalami error atau kendala.

- **Melihat System Log Real-time Berjalan (Journald):**

```bash
sudo journalctl -f

```

- **Melihat Log Spesifik pada Satu Service (Contoh: Nginx):**

```bash
sudo journalctl -u nginx -f

```

- **Melihat Log Autentikasi dan Upaya Login Sistem:**

```bash
sudo tail -f /var/log/auth.log

```

- **Melihat Pesan Log Umum Sistem:**

```bash
sudo tail -f /var/log/syslog

```

#### LANGKAH 4.7: Audit Otomatisasi Pekerjaan (Cron Job)

Langkah untuk melihat jadwal backup, skrip berkala, atau sinkronisasi data otomatis.

- **Memeriksa Cron Job milik User yang Sedang Aktif:**

```bash
crontab -l

```

- **Memeriksa Cron Job milik Superuser (Root):**

```bash
sudo crontab -l -u root

```

---

### 5. TINDAKAN PASCA-AUDIT (POST-AUDIT ACTIONS)

1. **Pengisian Inventaris Server:** Salin luaran (output) dari perintah di atas ke dalam _repository_ Git menggunakan _template_ Markdown inventaris per-server (Lihat Lampiran A).
2. **Evaluasi Cadangan (Backup Plan):** Pastikan proses backup otomatis sudah berjalan. Jika tidak ditemukan cron job backup, ajukan segera pembuatan skrip backup berkala.
3. **Penerapan Monitoring:** Direkomendasikan untuk memasang _monitoring agent_ (seperti Netdata, Prometheus Node Exporter, atau Zabbix) sesegera mungkin guna melacak kesehatan server secara proaktif.

---

### LAMPIRAN A: TEMPLATE INVENTARIS SERVER (Simpan sebagai `server-name.md`)

markdown

# Inventaris Server: [Nama-Server / Hostname]

## 1. Spesifikasi Umum

- **IP Address:** - **Sistem Operasi:** - **Versi Kernel:** - **Spesifikasi CPU:** - **Kapasitas RAM:** - **Kapasitas Storage:** ## 2. Service Utama & Port Terbuka
  | Port | Service / Aplikasi | Fungsi / Deskripsi | Pengelola (Systemd/Docker) |
  |---|---|---|---|
  | 22 | SSH | Remote Akses | Systemd |
  | | | | |

## 3. Daftar User dengan Akses Sudo

- [Nama User 1]
- [Nama User 2]

## 4. Jadwal Backup & Cron Job Pemeliharaan

- [Tulis jadwal jika ada, atau "Belum Ada"]

```

```

```

```

```

```
