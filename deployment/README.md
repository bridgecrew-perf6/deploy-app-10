# Tutorial Deployment Node.js Dengan Heroku

Pertama - tama :

- Daftar akun heroku
- Download dan install [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli)

Kita akan memakai proyek sebelumnya yang berada pada folder authentication.

Copy isi dari folder authentication kedalam folder git baru, atau repositori baru baik itu gitlab / github. Setelah itu jangan lupa _commit_ terlebih dahulu semua file yang akan kit upload ke dalam server Heroku.

Lakukan login dengan heroku CLI pada terminal kalian masing - masing


```
$ heroku login
```

Jikalau ini login memakai password kita bisa pakai opsi `-i`



```
$ heroku login -i
```

## Login Berhasil, Saatnya Deployment!

Sebelum kita merilis aplikasi dengan heroku, buat file dengan nama `Procfile` dan isi dengan `web: npm run start`. Nantinya Heroku akan mengeksekusi ini saat perilisan aplikasi kita.

```txt
# File Procfile
web: npm run start
```

Untuk jalannya port aplikasi nanti, kita perlu menggantinya dengan :


```js
const port = process.env.PORT || 8000
```

Dengan begini Heroku akan otomatis menyediakan port pada aplikasi kita nantinya.

Setelah hal diatas sudah dilakukan, kita harus membuat proyek di heroku dengan cara jalankan ini di terminal :

```
$ heroku create # lakukan ini di dalam git repositori kita
```

Kita akan mendapatkan link url web kita, setelah itu coba buka dulu. Jika sudah bisa terbuka kalian akan melihat halaman web sederhana dari heroku.

Setelah itu untuk deployment kita cukup menuliskan perintah ini pada terminal

```
$ git push heroku main
```

Maka aplikasi kalian akan ter-_deploy_ dengan mudahnya.

> Jagan lupa simpan dulu (git commit dan push) kerjaan kita yaah.

## Database di Heroku

Jika kita coba aplikasi kita ~ pada saat login akan terjadi error, cara melihat error tersebut adalah dengan `heroku logs`, bisa dijalankan di terminal, agar heroku logs berjalan terus pada terminal kita, kita bisa pakai opsi `--tail`.

```
$ heroku logs --tail
```

Aplikasi kita akan error karena tidak bisa terkoneksi database.

Untuk membuat database pada heroku lakukan langkah berikut

1. Ke Heroku Dashboard
2. Pilih Project Kalian
3. Klik `configure add on`
4. Ketik `Postgres`
5. Pilih database lalu opsi yang free saja

Balik lagi ke dasboard proyek, pilih settings lalu kailian akan menemukan opsi `Reveal Config Vars`, klik tombol tersebut lalu kita akan mendapatkan config `DATABASE_URL`.

Pakai config tersebut, untuk mengganti koneksi ke database

Dari seperti ini

```js
const pool = new Pool({
  host: 'localhost',
  user: 'postgres',
  password: 'postgres',
  database: 'binar_fsw_auth_tutorial',
  max: 20,
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
})
```

ke seperti ini, 


```js
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: { rejectUnauthorized: false }
})
```

Setelah aplikasi kita bisa terkoneksi, jangan lupa untuk migrasi dulu databasenya. Migrasi ini bisa dilakukan dengan manual, dengan menjalankan perintah sql migrasi kita ke dalam database, baik itu melalui tool seperti DBeaver ataupun dengan login ke database melalui heroku cli.

```
$ heroku pg:psql postgresql-elliptical-20554 --app limitless-dawn-39233
```

lalu jalankan migrasinya disana.

Oke, Setelah ini kita hanya perlu commit dan push dulu perubahan kita dan lakukan deployment lagi

```
git push heroku main
```

Selamat aplikasi kita berjalan dengan flamboyan!
