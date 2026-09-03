# Install Docker Manual (examp: for ubuntu 22.04.05)

## Step

### 1. update package dan hapus docker lama jika ada

```bash
sudo apt update
sudo apt remove -y docker docker-engine docker.io containerd runc
```

### 2. install dependency

```bash
sudo apt install -y ca-certificates curl
```

### 3. Tambahkan GPG key docker

```bash
sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### 4. Tambahkan repository Docker

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Kemudian

```bash
sudo apt update
```

### 5. install docker engine + compose

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

cek

```bash
sudo systemctl status docker
```

seharusnya muncul:

```
Active: active (running)
```

cek versi docker:

```bash
sudo docker --version
sudo docker compose version
```

### (optional) 6. Buat agar docker bisa berjalan tanpa sudo

```bash
sudo usermod -aG docker $USER
```

kemudian logout, dan login ssh kembali

cek:

```bash
docker ps
```

### (optional) pastikan docker otomatis start pada saat setelah reboot

```bash
sudo systemctl enable docker
sudo systemctl enable containerd
```
