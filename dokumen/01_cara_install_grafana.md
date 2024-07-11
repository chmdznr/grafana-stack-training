### Langkah-langkah Instalasi Grafana di Ubuntu 22.04

1. **Update Sistem:**
   Pertama, pastikan sistem Anda diperbarui dengan perintah berikut:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Instalasi Paket Pendukung:**
   Instal beberapa paket yang dibutuhkan:
   ```bash
   sudo apt install -y software-properties-common apt-transport-https wget
   ```

3. **Tambahkan Repository Grafana:**
   Tambahkan kunci GPG resmi Grafana dan repository Grafana ke sistem Anda:
   ```bash
   wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
   sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
   ```

4. **Instalasi Grafana:**
   Instal Grafana dengan perintah berikut:
   ```bash
   sudo apt update
   sudo apt install grafana -y
   ```

5. **Mulai dan Aktifkan Layanan Grafana:**
   Mulai layanan Grafana dan aktifkan agar berjalan otomatis saat sistem dinyalakan:
   ```bash
   sudo systemctl start grafana-server
   sudo systemctl enable grafana-server
   ```

6. **Verifikasi Instalasi:**
   Verifikasi bahwa Grafana sudah berjalan:
   ```bash
   sudo systemctl status grafana-server
   ```

### Lokasi File Konfigurasi Grafana

File konfigurasi utama Grafana biasanya berada di:
```
/etc/grafana/grafana.ini
```

### Poin-Poin Penting dari Konfigurasi Grafana

1. **Port:**
   Secara default, Grafana berjalan di port 3000. Anda bisa mengubah port ini dengan mengedit bagian `[server]` di file `grafana.ini`:
   ```ini
   [server]
   http_port = 3000
   ```

2. **Database:**
   Grafana menggunakan SQLite secara default untuk menyimpan data. Anda bisa mengubah database ke MySQL, PostgreSQL, atau yang lainnya dengan mengubah bagian `[database]`:
   ```ini
   [database]
   type = sqlite3
   path = grafana.db
   ;host = 127.0.0.1:3306
   ;name = grafana
   ;user = grafana
   ;password = grafana
   ```

3. **Security:**
   Bagian `[security]` memungkinkan Anda mengatur pengaturan keamanan seperti admin user dan password:
   ```ini
   [security]
   admin_user = admin
   admin_password = admin
   ```

4. **SMTP/Email:**
   Untuk mengirim notifikasi email, Anda harus mengkonfigurasi bagian `[smtp]`:
   ```ini
   [smtp]
   enabled = true
   host = smtp.example.com:587
   user = your_user
   password = your_password
   from_address = grafana@example.com
   from_name = Grafana
   ```

5. **Data Sources:**
   Anda perlu menambahkan sumber data (data sources) dari antarmuka web Grafana setelah instalasi. Hal ini bisa dilakukan melalui menu "Configuration" di dashboard Grafana.