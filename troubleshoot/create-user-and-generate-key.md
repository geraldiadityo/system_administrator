# Generate new user and key for login ssh

## Create new user

- buat user baru

```bash
sudo useradd -m -s /bin/bash {username}
```

penjelasan
| Parameter | Arti |
| --------- | ---------------------- |
| `-m` | membuat home directory |
| `-s` | menentukan shell |

- verifikasi

```bash
sudo /etc/passwd | gred {username}
```

output

```
{username}:x:1002:1002::/home/{username}:/bin/bash
```

## jangan buat password untuk user tersebut

biasanya orang akan membuat password untuk sebuah user

```bash
sudo passwd {username}
```

tapi kali ini tidak perlu, karena nanti login akan menggunakan key.
dan juga password nya di kunci

- untuk mengunci password

```bash
sudo passwd -l {username}
```

## membuat folder .ssh untuk user

masuk sebagai root

- buat folder ssh nya

```bash
sudo mkdir /home/{username}/.ssh
```

- atur kepemilikan dari folder tersebut

```bash
sudo chown {username}:{groupusername} /home/{username}/.ssh
```

- atur permission

```bash
sudo chmod 700 /home/{username}/.ssh
```

check hasilnya

```bash
ls -ld /home/{username}/.ssh
```

outputnya

```
drwx------ {username} {groupusername}
```

## generate ssh key nya

generate sebagai user tersebut

```bash
sudo -u username ssh-keygen \
-t ed25519 \
-f /home/{username}/.ssh/id_ed25519 \
-N ""
```

penjelasan

```
-t ed25519
```

jenis key

```
-f
```

tempat peyimpanan key

```
-N ""
```

tidak menggunakan passphrase

hasil

```
id_ed25519
id_ed25519.pub
```

## membuat authorized_keys

salin public key dan masukkan ke authorized_keys

```bash
cat /home/{username}/.ssh/ed_25519.pub > /home/{username}/.ssh/authorized_keys
```

## permission

folder

```bash
chmod 700 /home/{username}/.ssh
```

authorized_keys

```bash
chmod 600 /home/{username}/.ssh/authorized_keys
```

private key

```bash
chmod 600 /home/{username}/.ssh/id_ed25519
```

public key

```bash
chmod 644 /home/{username}/.ssh/id_ed25519.pub
```

ownership

```bash
chown -R {username}:{group} /home/{username}/.ssh
```

## export private keys

buat 1 folder untuk penampung keys
misalnya: /opt/exported_keys

```bash
sudo mkdir -p /opt/exported_keys
```

- copy key ke folder tersebut

```bash
sudo cp /home/{username}/.ssh/id_ed25519 /opt/exported_keys/{username}.pem
```

- permission

```bash
chmod 600 /opt/exported_keys/{username}.pem
```

## hapus private key dari server

- hapus private key

```bash
rm /home/{username}/.ssh/id_ed25519
```
