```garage.toml
# kita bebas menentukan lokasi penyimpanan
metadata_dir = "/tmp/meta"
# kita bebas menentukan lokasi penyimpanan
data_dir = "/tmp/data"
# digunakan pada saat mode development
db_engine = "sqlite" 
# digunakan pada saat mode production
# db_engine = "lmdb" 

# ini berarti tanpa replikasi. digunakan pada mode development
replication_factor = 1
# 3 berarti 3 replikasi node. ini merupakan jumlah stardart node pada mode production
# replication_factor = 3 

# menerima paket dari antarmuka/ip manapun baik IPv4 atau IPv6
rpc_bind_addr = "[::]:3901"
# alamat yang bisa diakses oleh node lain, bisa ip public atau ip private
# ini juga bisa berupa url, contoh: portalberita.co.id:3901
rpc_public_addr = "127.0.0.1:3901"
# openssl rand -hex 32
# jalankan untuk membuat rpc_secret. pastikan semua node dalam cluster yang sama memiliki rpc_secret yang sama juga
rpc_secret = "....."

[s3_api]
s3_region = "garage"
api_bind_addr = "[::]:3900"
root_domain = ".s3.garage.localhost"

[s3_web]
bind_addr = "[::]:3902"
root_domain = ".web.garage.localhost"
index = "index.html"

[admin]
api_bind_addr = "[::]:3903"
admin_token = "$(openssl rand -base64 32)"
metrics_token = "$(openssl rand -base64 32)"
```