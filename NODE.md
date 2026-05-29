# Node

## Node ID
pada sistem terdistribusi tradisional, server sering dikenali berdasarkan alamat IP nya. di garage, alamat IP bisa berubah-ubah, jadi garage tidak menggunakan IP tapi menggunakan kriptografi asimetris (Ed25519) untuk identitas

- saat pertama kali kamu menjalankan perintah `garage server` sistem akan menghasilkan sebuah private key yang disimpan di dalam folder metadata_dir (biasanya bernama node_key)

- dari private key tersebut, garage menghasilkan public key berwujud deretan karakter hexadecimal (misalnya: d8a9f...). public key inilah yang menjadi Node ID

- Node ID ini berfungsi ketika node kamu berbicara dengan node lain, mereka akan saling memverifikasi identitas menggunakan node ID ini. jika IP server berubah, node lain tetap mengenalinya karena Node ID nya tidak berubah

## Penyimpanan (Metadata & Data)
sebuah node secara teknis memisahkan pekerjaannya menjadi dua direktori yang menggunakan teknologi berbeda, yaitu metadata_dir dan data_dir.

### metadata_dir
direktori ini tidak menyimpan file foto atau video kamu. Ini adalah database internal berkecepatan sangat tinggi. garage menggunakan engine bernama LMDB (Lightning Memory-Mapped Database). LMDB bertugas mencatat siapa pemilik bucket, kunci akses, daftar file, dan di node mana file tersebut disimpan. karena ukurannya kecil namun butuh akses cepat, sangat disarankan meletakkan direktori ini di penyimpanan SSD/NVMe. 

### data_dir
di sinilah fisik file disimpan. namun, jika kamu melihat isi folder ini melalui terminal, kamu tidak akan menemukan file    `kebakaran.jpg`. garage menggunakan konsep Content-Addressable Storage (CAS). file kamu dipecah menjadi blok-blok kecil (biasanya maksimal 1MB), di-enkripsi secara internal, di-hash (misalnya menggunakan SHA-256), lalu disimpan dengan nama file berupa kode hash tersebut. hal ini mencegah adanya duplikasi data (jika kamu mengunggah 2 file yang isinya sama persis, garage hanya akan menyimpannya satu kali)

kamu juga bisa merubah lokasi metadata_dir dan data_dir sesuai keinginan

## Komunikasi
Sebuah Node memiliki dua "telinga" jaringan utama yang selalu mendengarkan:

### API Port (Port 3900)
menangani komunikasi dengan dunia luar menggunakan protokol HTTP/S3 standar

### RPC Port (Port 3901)
ini adalah jalur belakang yang menggunakan protokol khusus berbasis TCP. port ini hanya digunakan untuk komunikasi antar node. komunikasi ini diamankan dan dienkripsi menggunakan parameter rpc_secret yang di buat dengan OpenSSL pada saat instalasi garage

## Instalasi
- download garage [disini](https://garagehq.deuxfleurs.fr/download/), copy link dan download sesuai arsitektur menggunakan wget atau yang lain
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
- buat rpc_secret menggunakan openssl kemudian simpan sementara di notepad. ini akan kita gunakan nanti
```
openssl rand -hex 32
```

- buat folder untuk metadata dan data. kita bebas menentukan lokasi penyimpanan
```
sudo mkdir -p /var/lib/garage/metadata
sudo mkdir -p /var/lib/garage/data
```

- buat file `/etc/garage.toml` dengan isi sebagai berikut [configuration example](CONFIGURATION_EXAMPLE.md)

