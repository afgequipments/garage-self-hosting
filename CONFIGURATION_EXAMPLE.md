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
# nama daerah virtual yang menggambarkan lokasi server saat ini. 
# kamu bisa merubah ini sesuai keinginan, contoh: bws-1, jbr-1, jbr-2 
s3_region = "garage"
# menerima semua permintaan dari luar melalui port 3900
# samakan nilainya jika ini berada pada cluster yang sama
api_bind_addr = "[::]:3900"
# ganti sesuai domain yang digunakan
# samakan nilainya jika ini berada pada cluster yang sama
root_domain = ".s3.garage.localhost"

[s3_web]
# membuka port 3902 di OS untuk menerima koneksi HTTP web browser biasa
# cocok dijadikan sebagai hosting website static
bind_addr = "[::]:3902"
# domain utama khusus untuk web hosting
# jika bucket Anda bernama "katalog", maka URL webnya menjadi katalog.web.garage.localhost
root_domain = ".web.garage.localhost"
# file default yang akan otomatis dieksekusi saat pengunjung membuka URL root (tanpa menyebut nama file spesifik)
index = "index.html"

[admin]
api_bind_addr = "[::]:3903"
# openssl rand -base64 32
# jalankan untuk membuat admin_token. pastikan semua node dalam cluster yang sama memiliki admin_token yang sama juga
admin_token = "......."
# openssl rand -base64 32
# jalankan untuk membuat admin_token. pastikan semua node dalam cluster yang sama memiliki admin_token yang sama juga
metrics_token = "......"
```