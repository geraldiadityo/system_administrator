# Generate Cert SSL dan private key

## CASE

sebuah instansi/kantor memilki sebuah server tempat running sebuah aplikasi
aplikasi tersebut adalah aplikasi pusat yang dapat di instance oleh mereka untuk sebuah instansi
jadi mereka menginstace sebuah aplikasi di server mereka tetapi aplikasi ini di gunakan private oleh sebuah instansi
\*note: dengan catatan, domain/dns di sediakan oleh pengguna aplikasi

## Problem

penyedia aplikasi meminta admin pengguna aplikasi untuk generate SSL, agar aplikasi berjalan di bawah protocol SSL/TLS.
dengan begitu, penyedia aplikasi meminta beberapa file untuk aplikasi agar dapat berjalan di bawah protol SSL/TLS
file yang di minta adalah

- file.cert (sertifikat ssl)
- private.key (private key dari ssl tersebut)

## Solver

dengan indikasi

- domain di kelola oleh pengguna aplikasi (cloudflare)

maka metode solver yang di gunakan adalah generate ssl cert menggunakan API Token dari cloudflare untuk generate SSL berdasarkan domain/subdomain yang digunakan
atau bisa di sebut dengan dns challenge cloudflare API TOKEN

## step by step

- pastikan sudah menginstall python3

```bash
python3 --version
```

- buat virtual environtment agar tidak mengganggu dependency local lain

* linux/macOs

```bash
python3 -m venv venv
```

- windows

```bash
python -m venv venv
```

- aktifkan virtual environtment nya

* linux/macOs

```bash
source venv/bin/activate
```

- windows

```cmd
venv\Scripts\activate.bat
```

```PowerShell
venv\Scripts\Activate.ps1
```

- upgrade pip

```bash
pip install --upgrade pip setuptools wheel
```

- install certbot dan plugin cloudflare

```bash
pip install certbot certbot-dns-cloudflare
```

- buat Cloudflare api token

Di cloudflare buat API token, bukan global API key
Permission:

```
Zone
    DNS
        Edit

Zone
    Zone
        Read
```

Zone Resources:

```
Include
Specific Zone
example.com
```

- simpan Cloudflare API Token

* buat folder

```bash
mkdir secrets
```

- buat file

```
secrets/
└── cloudflare.ini
```

- isi

```INI
dns_cloudflare_api_token = YOUR_API_TOKEN
```

- ubah permission

```bash
chmod 600 secret/cloudflare.ini
```

- generate cert

misalkan domain nya : example.com

```bash
certbot certonly \
  --dns-cloudflare \
  --dns-cloudflare-credentials secrets/cloudflare.ini \
  --config-dir certs/config \
  --work-dir certs/work \
  --logs-dir certs/logs \
  -d example.com \
  -d "*.example.com"
```

- hasil

```
ssl-generator/
│
├── certs/
│   ├── config/
│   │   ├── archive/
│   │   ├── live/
│   │   │   └── example.com/
│   │   │       ├── cert.pem
│   │   │       ├── chain.pem
│   │   │       ├── fullchain.pem
│   │   │       └── privkey.pem
│   │   └── renewal/
│   │
│   ├── work/
│   └── logs/
│
├── secrets/
│   └── cloudflare.ini
│
├── requirements.txt
└── venv/
```

- yang di gunakanan

* fullchain.pem (cert)
* privkey.pem (private key)
