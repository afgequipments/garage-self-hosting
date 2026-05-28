# Garage Storage Self-Hosting

## Pengertian
[garage](https://garagehq.deuxfleurs.fr/) merupakan object storage terdistribusi yang menyimpan data sebagai object di dalam sebuah wadah yang disebut bucket. setiap object memiliki data, metadata dan ID uniknya sendiri. ini merupakan teknologi yang sama yang digunakan oleh Amazon S3 (Simple Storage Service). garage dirancang untuk bisa berjalan di banyak server (node) di lokasi yang berbeda dan bisa saling bekerja sama seolah menjadi sebuah hardisk raksasa.

## Filosofi
- mampu berjalan di VPS murah, server bekas, bahkan Raspberry Pi
- kompatible dengan S3
- tangguh dalam kendala jaringan karena dapat berjalan di lebih dari 1 server sehingga bisa tetap melayani permintaan ketika server lain sedang down

## Kebutuhan Perangkat Keras
- CPU dari 10 tahun terahir (x86_64, ARM)
- RAM 1 Gb
- SSD/HDD 16 Gb
- Network 200ms atau kurang, 50Mbps atau lebih

## Konsep Inti
### [Node](NODE.md)
satu server yang menjalankan aplikasi garage 

### Cluster
gabungan dari beberapa node yang saling terhubung. didalam cluster node akan saling berkomunikasi untuk memastikan status satu sama lain. node berkomunikasi menggunakan protocol RPC pada port 3901. 

- RPC (Remote Procedure Call) adalah protokol komunikasi yang memungkinkan suatu program komputer menjalankan fungsi di komputer lain, seolah-olah fungsi tersebut berjalan secara lokal

### Layout
sebuah denah yang dibuat oleh garage untuk mengingat dimana sebuah object disimpan. saat node baru ditambahkan, maka layout akan diperbarui

### Replication
secara default garage akan menyimpan 3 salinan dari setiap object yang di unggah dan meletakkannya ke node yang berbeda. inilah yang membuat layanan tetap berjalan jika misalkan salah satu node mengalami kendala atau sedang diperbaiki

### Gateway
pintu masuk untuk aplikasi lain. garage membuka API S3 di port 3900

## Simulasi Implementasi
bayangkan kamu memiliki sebuah perusahaan portal berita bernama RRQ. website ini memuat ribuan foto berita setiap harinya dan kamu ingin penyimpanan yang aman, tidak boleh ada foto yang hilang, tapi kamu sedang berhemat sehingga tidak bisa sewa server cloud yang mahal, melainkan hanya mampu sewa tiga buah VPS murah di tiga lokasi berbeda:
- Server A (di bondowoso)
- Server B (di jember)
- Server C (di banyuwangi)

ayo kita lihat bagaimana konsep inti garage bekerja dalam skenario ini:

### 1. Node (Server Individu)
pada masing-masing server (A, B, C) kamu menginstal aplikasi garage di ubuntu. setiap server yang menjalankan program garage ini disebut sebagai node. Saat ini mereka adalah tiga node yang terisolasi dan tidak saling mengenal

### 2. Cluster (Satu Kesatuan)
agar ketiga node ini bisa bekerja sama, kamu harus menggabungkan mereka. kamu perlu memberikan konfigurasi yang sama dan mengatur agar mereka saling mengobrol melalui jaringan internet (via RPC Port 3901). gabungan dari node A, B, dan C ini sekarang membentuk sebuah cluster. bagi website RRQ, cluster ini terlihat seperti satu hardisk raksasa virtual bukan lagi tiga server yang terpisah

### 3. Gateway (Pintu Masuk Aplikasi)
website RRQ tidak peduli dengan kerumitan cluster kamu. yang website tahu hanyalah cara mengirim file melalui standar S3. karena itu, garage menyediakan gateway (default port 3900). kita bayangkan saja gateway ini adalah resepsionis. ketika reporter mengunggah foto `kebakaran.jpg`, website akan mengetuk pintu gateway di server A untuk menyerahkan file tersebut beserta kunci aksesnya (AK/SK)

### 4. Layout / Ring (Buku Peta)
setelah server A menerima `kebakaran.jpg` dari gateway, ia tidak langsung menyimpannya secara asal. server A akan melihat layout terlebih dahulu.
layout adalah semacam peta atau buku besar yang dimiliki oleh setiap node. peta ini berisi aturan tentang pembagian tugas. karena kamu memiliki 3 node, layout ini memastikan beban penyimpanan terbagi rata secara adil berdasarkan kapasitas masing-masing hardisk node

### 5. Replikasi (Penggandaan untuk Keamanan)
ini merupakan inti utama garage. secara bawaan, garage menggunakan tingkat replikasi "3". setelah server A melihat layout, ia menyadari bahwa foto `kebakaran.jpg` harus sangat aman. maka dari itu, server A akan menyimpan satu salinan di hardisknya sendiri, kemudian mereplikanya (mengirimkan salinan) ke server B dan Server C. Kini, ketiga server memiliki foto yang sama persis.

## Simulasi Bencana / Error
suatu hari, server A mengalami perbaikan total. apa yang terjadi pada web portal berita kamu?

### 1. Gateway Berpindah
server A offline, sistem secara otomatis mengalihkan permintaan ke gateway server B

### 2. Data Tetap Aman
saat pengunjung web ingin melihat foto `kebakaran.jpg`, server B akan melihat buku petanya (layout). ia tahu server A sedang offline, tetapi ia juga tahu bahwa ia sendiri memiliki salinan dari foto tersebut berkat proses replikasi sebelumnya

### 3. Tanpa Downtime
server B langsung memberikan foto tersebut kepada pengunjung. pengunjung tidak menyadari bahwa salah satu server kamu sedang offline

### 4. Pemulihan Otomatis
ketika server A online kembali, ia akan "bertanya" kepada server B dan C "apakah ada file baru saat aku offline kemarin?". server B dan C akan mengirimkan data yang tertinggal ke server A secara otomatis hingga mereka semua memiliki data yang sama kembali (sinkronisasi)

inilah alasan mengapa garage sangat cocok untuk self-hosting dengan infrastruktur yang tidak sempurna. kamu bisa membangun ketahanan tingkat enterprise hanya dengan server murah dan mesin bekas

## Pertanyaan
- bagaimana melakukan instalasi garage pada server ubuntu atau server lain ?

- bagaimana melakukan konfigurasi cluster?

- apakah gateway perlu di konfigurasi atau ini akan otomatis tanpa perlu konfigurasi?

- 

