# set timezone and sync with chrony

## CASE

sebuah server selalu tidak sync waktu nya dengan pengguna aplikasi di dalam server tersebut

## Problem

ketika menginput tanggal dan waktu ke dalam aplikasi atau mengambil data waktu maka tidak seperti yang di inginkan

## Solver

set timezone menjadi timezone dimana aplikasi di gunakan, aktifkan ntp client dan gunakan chrony untuk mengganti ntpd

## step by step

- cek timezone saat ini

```bash
timedatectl
```

contoh output

```
Local time: Sat 2026-07-18 19:55:12 UTC
Universal time: Sat 2026-07-18 19:55:12 UTC
RTC time: Sat 2026-07-18 19:55:12
Time zone: Etc/UTC (UTC, +0000)
System clock synchronized: yes
NTP service: active
```

- lihat daftar timezone yang tersedia

```bash
timedatectl list-timezones
```

atau bisa mencari berasarkan negara, misalnya indonesia:

```bash
timedatectl list-timezones | grep Asia
```

contoh output

```
Asia/Jakarta
Asia/Makassar
Asia/Jayapura
```

- mengubah timezone

misalnya ingin mengubah ke WIB (UTC + 7)

```bash
sudo timedatectl set-timezone Asia/Jakarta
```

- verifikasi

cek kembali zona waktu

```bash
timedatectl
```

output:

```
Local time: Sun 2026-07-19 02:56:15 WIB
Universal time: Sat 2026-07-18 19:56:15 UTC
RTC time: Sat 2026-07-18 19:56:15
Time zone: Asia/Jakarta (WIB, +0700)
System clock synchronized: yes
NTP service: active
```

- pastikan sinkronasi waktu (NTP) aktif

cek status

```bash
timedatectl status
```

jika belum aktif

```bash
sudo timedatectl set-ntp true
```

seharusnya status:

```
System clock synchronized: yes
NTP service: active
```

- aktifkan chrony agar sinkronasi lebih optimal (optional)

* update dan upgrade

```bash
sudo apt update
sudo apt upgrade -y
```

- install chrony

```bash
sudo apt install chrony
```

- check status chrony

```bash
sudo systemctl status chrony
```

jika belum aktif

```bash
sudo systemctl enable --now chrony
```

- sync

```bash
sudo chronyc -a makestep
```
