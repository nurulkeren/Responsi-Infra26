Nama: Nurul Maftuhah
NIM: H1H024002
SHIFT: A

# Analisis Perbaikan

## Permasalahan 1

### Gejala

Docker Compose gagal dijalankan karena file konfigurasi tidak dapat diparsing dengan benar.

### Penyebab

Pada file `docker-compose.yml` ditemukan kesalahan penulisan bagian service, yaitu:

```
services
```

Penulisan tersebut tidak menggunakan tanda titik dua (`:`) sehingga tidak sesuai dengan sintaks Docker Compose.

### Solusi

Konfigurasi diperbaiki menjadi:

```
services:
```

Setelah diperbaiki, Docker Compose dapat membaca daftar service yang akan dijalankan dengan benar.

---

## Permasalahan 2

### Gejala

Container `web1` tidak dapat terhubung ke database MySQL.

### Penyebab

Pada service `web1` ditemukan konfigurasi hostname database yang tidak sesuai:

```
DB_HOST: mysql
```

Sedangkan service database didefinisikan dengan nama:

```
db:
```

Docker Compose menggunakan nama service sebagai hostname internal jaringan sehingga hostname yang digunakan harus sesuai dengan nama service yang didefinisikan.

### Solusi

Hostname database diperbaiki menjadi:

```
DB_HOST: db
```

Setelah diperbaiki, service `web1` dapat terhubung ke database dengan benar.

---

## Permasalahan 3

### Gejala

Aplikasi pada container `web2` gagal melakukan koneksi ke database.

### Penyebab

Password database yang digunakan pada service `web2` tidak sesuai dengan password database yang sebenarnya.

Konfigurasi yang ditemukan:

```
DB_PASS: wrongpassword
```

Sedangkan password database yang benar adalah:

```
MYSQL_PASSWORD: student123
```

### Solusi

Password diperbaiki menjadi:

```
DB_PASS: student123
```

Setelah perbaikan, service `web2` dapat melakukan autentikasi dan terhubung ke database dengan normal.

---

## Permasalahan 4

### Gejala

Proses build image untuk container `web3` gagal dijalankan.

### Penyebab

Docker Compose tidak dapat menemukan direktori build karena terdapat kesalahan penulisan path:

```
context: ./web33
```

Padahal direktori yang tersedia adalah:

```
context: ./web3
```

### Solusi

Path build diperbaiki menjadi:

```
context: ./web3
```

Setelah diperbaiki, image `web3` berhasil dibangun dan container dapat dijalankan.

---

## Permasalahan 5

### Gejala

Nginx tidak dapat mendistribusikan request ke container `web3`.

### Penyebab

Container `web3` hanya terhubung ke network backend:

```
networks:
  - backend
```

Akibatnya container `web3` tidak dapat dijangkau oleh Nginx yang berada pada network frontend.

### Solusi

Konfigurasi network diperbaiki menjadi:

```
networks:
  - frontend
  - backend
```

Setelah perbaikan, Nginx dapat melakukan load balancing ke `web3`.

---

## Permasalahan 6

### Gejala

Volume database tidak dapat digunakan dengan benar dan berpotensi menyebabkan data tidak tersimpan secara persisten.

### Penyebab

Nama volume yang digunakan pada service database berbeda dengan deklarasi volume yang tersedia.

Pada service database:

```
volumes:
  - db-data:/var/lib/mysql
```

Sedangkan deklarasi volume:

```
volumes:
  database-data:
```

### Solusi

Nama volume disamakan menjadi:

```
volumes:
  db-data:
```

Setelah diperbaiki, data MySQL dapat tersimpan secara permanen melalui Docker Volume.

---

## Permasalahan 7

### Gejala

Container MySQL gagal dijalankan dan berhenti dengan status error.

### Penyebab

File `init.sql` masih mengandung sintaks markdown sehingga tidak dapat diproses oleh MySQL saat proses inisialisasi database.

### Solusi

Seluruh sintaks markdown dihapus sehingga file hanya berisi perintah SQL yang valid. Setelah diperbaiki, database berhasil dibuat dan container MySQL dapat berjalan dengan normal.

---

## Permasalahan 8

### Gejala

Container Nginx gagal dijalankan dan menampilkan pesan error:

````
unknown directive "```nginx"
````

### Penyebab

File konfigurasi `nginx.conf` masih mengandung sintaks markdown yang tidak dikenali sebagai directive Nginx.

### Solusi

Seluruh sintaks markdown dihapus sehingga file hanya berisi konfigurasi Nginx yang valid. Setelah diperbaiki, Nginx berhasil membaca konfigurasi dan dapat dijalankan.

---

## Permasalahan 9

### Gejala

Container Nginx gagal dijalankan dengan pesan:

```
host not found in upstream "web11:80"
```

### Penyebab

Terdapat kesalahan penulisan nama host pada konfigurasi upstream Nginx:

```
server web11:80;
```

Padahal nama container yang benar adalah:

```
server web1:80;
```

### Solusi

Hostname diperbaiki menjadi:

```
server web1:80;
```

Setelah diperbaiki, Nginx berhasil melakukan resolusi hostname dan load balancer dapat berjalan dengan normal.

---

# Hasil Akhir

### Gejala

Sebelum dilakukan perbaikan, sebagian container gagal dijalankan sehingga layanan web dan load balancer tidak dapat diakses dengan baik.

### Penyebab

Terdapat beberapa kesalahan konfigurasi pada Docker Compose, konfigurasi jaringan, volume, file SQL, dan konfigurasi Nginx.

### Solusi

Seluruh kesalahan konfigurasi berhasil diperbaiki dan sistem dijalankan kembali menggunakan Docker Compose.

Hasil pengujian menunjukkan bahwa seluruh container (`nginx-lb`, `web1`, `web2`, `web3`, dan `mysql-db`) berhasil berjalan dengan status **Up**. Pengujian load balancing menggunakan perintah:

```
for i in {1..10}; do curl -s localhost:8080 | grep "WEB SERVER"; done
```

menunjukkan hasil yang bergantian antara **WEB SERVER 1**, **WEB SERVER 2**, dan **WEB SERVER 3**. Hal ini membuktikan bahwa mekanisme load balancing Nginx dengan metode **round robin** telah berfungsi dengan baik dan seluruh layanan dapat beroperasi sesuai dengan kebutuhan sistem.

