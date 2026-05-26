# single node

## OS
### ubuntu 24.04 LTS
- CPU 4 Core 
- RAM 4Gb 
- SSD 60Gb

## Mode
- local
- single node

## Install
- buka halaman download garage [disini](https://garagehq.deuxfleurs.fr/download/), copy link dan download sesuai arsitektur menggunakan wget atau yang lain
```
wget https://garagehq.deuxfleurs.fr/_releases/v2.3.0/x86_64-unknown-linux-musl/garage
```

- buat agar file bisa dieksekusi
```
chmod +x garage
```

- pindahkan garage agar bisa diakses dimanapun
```
sudo mv garage /usr/local/bin
```

- cek versi garage untuk memastikan proses install berhasil
```
garage --version
```

## Konfigurasi
- buat secret key menggunakan openssl
```
openssl rand -hex 32
```

- buat folder untuk metadata dan data
```
sudo mkdir -p /var/lib/garage/meta
sudo mkdir -p /var/lib/garage/data
```

- secara default garage akan mencari file konfigurasi di /etc/garage.toml, ayo kita buat file garage.toml di /etc
```
sudo nano /etc/garage.toml
```
```
# ganti path metadata & data ke partisi disk/mount point yang diinginkan
metadata_dir = "/var/lib/garage/meta"
data_dir = "/var/lib/garage/data"

# mode single node
replication_mode = "none"

# konfigurasi komunikasi antar node (RPC)
rpc_bind_addr = "[::]:3901"
rpc_public_addr = "127.0.0.1:3901"
rpc_secret = "paste hasil generate openssl tadi di sini" # openssl rand -hex 32
bootstrap_peers = []

# konfigurasi endpoint
[s3_api]
s3_region = "garage"
api_bind_addr = "[::]:3900"
root_domain = "domainanda.com" # ganti dengan domain jika ingin public access
```

- buat systemd agar bisa berjalan di background
```
sudo nano /etc/systemd/system/garage.service
```

- isi file garage.service dengan ini
```
[Unit]
Description=Garage Object Storage
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=/usr/local/bin/garage server
Restart=always
RestartSec=10
LimitNOFILE=65536
Environment="RUST_LOG=info"

[Install]
WantedBy=multi-user.target
```

- muat ulang daemon systemd, lalu jalankan garage
```
sudo systemctl daemon-reload
sudo systemctl enable garage
sudo systemctl start garage
```

- cek status untuk memastikan tidak ada error
```
sudo systemctl status garage
```

- cek status garage node
```
garage status
```

*bersambung...*