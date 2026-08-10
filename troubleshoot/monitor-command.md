# Monitoring command

## untuk melihat berapa lama sebuah komputer/server sudah menyala sejak terakhir kali boot/restart.

- perintah

```bash
uptime
```

- contoh hasil

```
09:26:31 up 12 days, 4:32, 2 users, load average: 0.35, 0.42, 0.38
```

- penjelasan
  | Bagian | Arti |
  | -------------------------------- | ------------------------------------------------------ |
  | `09:26:31` | Waktu saat ini |
  | `up 12 days, 4:32` | Server sudah menyala selama **12 hari 4 jam 32 menit** |
  | `2 users` | Ada 2 user yang sedang login |
  | `load average: 0.35, 0.42, 0.38` | Beban sistem rata-rata selama **1, 5, dan 15 menit** |

- catatan untuk load average server
  ini sering di pakai ketika trouble shooting server
  misalnya load server average 1.00, 1.20, 0.80
  ini artinya 1 core cpu sedang sibuk
  untuk melihat perbandingannya adalah, anggap rata2 hasil nya 1.00
  dan anda memilki core cpu 4
  maka cara perbandingan nya 1.00/4 = 25%
  maka masih aman

sedangkan jika load avg seperti ini

```
load average: 8.00, 7.50, 7.20
```

maka ini melebihi kapasitas core CPU

untuk melihat jumlah CPU

```bash
nproc
```

## untuk meliihat CPU usage

perintah

```bash
top -bn1 | grep "%Cpu(s):" | cut -d ',' -f 4 | awk '{print "Usage: " 100-$1 "%"}'
```

output

```
Usage: 2.1%
```

breakdown command

1. top -bn1
   menjalankan top mode non interaktif

- -b : batch mode, output berupa text sehingga bisa di pipe ke command lain.
- n1 : hanya mengambil 1 kali snapshoot

contoh output:

```
top - 03:32:59 up 22 days, 11:59,  1 user,  load average: 0.19, 0.32, 0.26
Tasks: 438 total,   1 running, 437 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.1 us,  0.7 sy,  0.0 ni, 97.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   7723.3 total,    345.9 free,   2613.6 used,   4763.8 buff/cache
MiB Swap:   4096.0 total,   3458.0 free,    638.0 used.   4793.0 avail Mem
...
```

2. | grep "%Cpu(s):"

- tanda | : berarti ambil output dari command sebelumnya dan dikirim sebagai input ke command selanjutnya

jadi

```bash
top -bn1 | grep "%Cpu(s):"
```

- grep : ambil line

penjelasnnya : dari hasil top -bn1 yang output nya di atas, ambil 1 line yang memiliki awalan "%Cpu(s):"

yang outputnya:

```
%Cpu(s):  2.1 us,  0.7 sy,  0.0 ni, 97.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st

```

yang artinya

| Singkatan | Nama               | Nilai | Artinya                                        |
| --------- | ------------------ | ----: | ---------------------------------------------- |
| `us`      | user               |  2.1% | CPU menjalankan program/aplikasi user          |
| `sy`      | system             |  0.7% | CPU menjalankan proses kernel Linux            |
| `ni`      | nice               |  0.0% | CPU menjalankan proses dengan prioritas `nice` |
| `id`      | idle               | 97.2% | CPU sedang tidak digunakan                     |
| `wa`      | I/O wait           |  0.0% | CPU sedang menunggu operasi I/O selesai        |
| `hi`      | hardware interrupt |  0.0% | CPU menangani interrupt dari hardware          |
| `si`      | software interrupt |  0.0% | CPU menangani software interrupt               |
| `st`      | steal              |  0.0% | CPU "diambil" hypervisor dari VM               |

disini terlihat data yang kita butuhkan untuk melihat hasil CPU Usage
yaitu 97.2 id : yang artinya adalah 97.2 CPU yang tersedia

3. | cut -d ',' -f 4

- cut : adalah perintah untuk memotong sebuah input dari line
- -d ',' : delimiter/pemisah yang disini contohnya : ',' berarti (,) adalah untuk pemisahnya
- -f 4 : ambil field ke 4 dari dari hasil delimiter tersebut

jadi

```bash
top -bn1 | grep "%Cpu(s):" | cut -d ',' -f 4
```

maka output dari:

```
%Cpu(s):  2.1 us,  0.7 sy,  0.0 ni, 97.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st

```

akan di cut, dipisah berdasarkan (,) dan mengambil field ke 4 yang hasilnya:
97.2 id

4. | awk '{print "Usage: " 100-$1 "%"}'

- awk : adalah perintah untuk memproses data string
- print : "Usage:" 100-$1 "%"
  perintah ini bisa di artikan seperti ini:

hasil dari perintah sebelumnya yaitu:
97.2 id

akan di proses sebagai $1 dan $2 -> $1: 97.2 dan $2: id

- 100 - $1 : karena 97.2 itu adalah idle/tersedia maka untuk untuk melihat penggunaan kita harus 100 dari total CPU - jumlah% CPU tersedia

maka hasilnya:

```bash
top -bn1 | grep "%Cpu(s):" | cut -d ',' -f 4 | awk '{print "Usage: " 100-$1 "%"}'
```

yang menghasilkan

```
Usage: 7.5%
```
