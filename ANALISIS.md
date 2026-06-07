Nama: Nurul Maftuhah
NIM: H1H024002
SHIFT: A

 Analisis Perbaikan Konfigurasi Docker

Pada proses implementasi Docker Compose, dilakukan analisis terhadap konfigurasi yang digunakan untuk menjalankan layanan web server, load balancer, dan database. Berdasarkan hasil pemeriksaan, ditemukan beberapa kesalahan konfigurasi yang menyebabkan sebagian container tidak dapat berjalan atau tidak dapat berkomunikasi dengan layanan lainnya.

### 1. Kesalahan Penulisan Service Docker Compose

Pada bagian awal file konfigurasi ditemukan penulisan:

```yaml
services
```

Penulisan tersebut tidak sesuai dengan sintaks Docker Compose karena tidak menggunakan tanda titik dua (`:`). Perbaikan dilakukan dengan mengubahnya menjadi:

```yaml
services:
```

Perbaikan ini diperlukan agar Docker Compose dapat membaca daftar service yang akan dijalankan.

### 2. Ketidaksesuaian Nama Host Database

Pada service `web1` ditemukan konfigurasi:

```yaml
DB_HOST: mysql
```

Sedangkan service database didefinisikan dengan nama:

```yaml
db:
```

Karena Docker Compose menggunakan nama service sebagai hostname internal, maka hostname yang benar adalah `db`. Oleh karena itu konfigurasi diperbaiki menjadi:

```yaml
DB_HOST: db
```

Perbaikan ini memungkinkan aplikasi web terhubung dengan database MySQL yang berada pada container database.

### 3. Kesalahan Password Database pada Web2

Pada service `web2` ditemukan konfigurasi:

```yaml
DB_PASS: wrongpassword
```

Sedangkan password database yang sebenarnya adalah:

```yaml
MYSQL_PASSWORD: student123
```

Ketidaksesuaian password menyebabkan aplikasi gagal melakukan autentikasi ke database. Oleh karena itu password diperbaiki menjadi:

```yaml
DB_PASS: student123
```

Setelah perbaikan, service web2 dapat terhubung dengan database secara normal.

### 4. Kesalahan Direktori Build pada Web3

Pada service `web3` ditemukan konfigurasi:

```yaml
context: ./web33
```

Padahal direktori yang tersedia adalah `web3`. Kesalahan ini menyebabkan proses build image gagal karena Docker tidak dapat menemukan direktori yang dimaksud.

Perbaikan dilakukan dengan mengubah konfigurasi menjadi:

```yaml
context: ./web3
```

### 5. Kesalahan Konfigurasi Jaringan Web3

Service `web3` hanya terhubung ke network backend:

```yaml
networks:
  - backend
```

Padahal service tersebut harus dapat menerima request dari Nginx yang berada pada network frontend. Oleh karena itu perlu ditambahkan network frontend:

```yaml
networks:
  - frontend
  - backend
```

Perbaikan ini memungkinkan Nginx melakukan load balancing ke web3.

### 6. Ketidaksesuaian Nama Volume

Pada service database digunakan volume:

```yaml
volumes:
  - db-data:/var/lib/mysql
```

Namun pada bagian deklarasi volume dituliskan:

```yaml
volumes:
  database-data:
```

Karena nama volume harus sama, maka dilakukan perbaikan menjadi:

```yaml
volumes:
  db-data:
```

Perbaikan ini memastikan data MySQL tersimpan secara persisten dan tidak hilang ketika container dihentikan atau dibuat ulang.

### Hasil Perbaikan

Setelah seluruh konfigurasi diperbaiki, seluruh container dapat dijalankan tanpa error. Nginx berhasil melakukan load balancing ke tiga web server, setiap web server dapat terhubung ke database MySQL, serta data database tersimpan secara permanen melalui Docker Volume. Selain itu komunikasi antar service menjadi lebih stabil karena konfigurasi jaringan telah sesuai dengan kebutuhan sistem.


