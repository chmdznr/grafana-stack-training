### Langkah-langkah Instalasi InfluxDB 2 di Ubuntu 22.04

1. **Update Sistem:**
   Pastikan sistem Anda diperbarui:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Download dan Instalasi Paket InfluxDB:**
   Unduh paket InfluxDB versi terbaru dan instal menggunakan `dpkg`:
   ```bash
   # Ubuntu/Debian AMD64
   curl -LO https://download.influxdata.com/influxdb/releases/influxdb2_2.7.6-1_amd64.deb
   sudo dpkg -i influxdb2_2.7.6-1_amd64.deb
   ```

3. **Mulai dan Aktifkan Layanan InfluxDB:**
   Mulai layanan InfluxDB dan atur agar berjalan otomatis saat sistem dinyalakan:
   ```bash
   sudo systemctl start influxdb
   sudo systemctl enable influxdb
   ```

### Konfigurasi Awal InfluxDB Menggunakan Web UI

1. **Akses UI InfluxDB:**
   Buka web browser dan akses UI InfluxDB di `http://localhost:8086`.

2. **Setup Awal:**
   - Ikuti wizard setup untuk mengkonfigurasi InfluxDB.
   - Masukkan detail berikut:
     - **Username**: Nama pengguna admin.
     - **Password**: Kata sandi pengguna admin.
     - **Organization Name**: Nama organisasi.
     - **Bucket Name**: Nama bucket default.
     - **Retention Period**: Periode retensi data untuk bucket (misalnya, 30 days).
   - Klik "Continue" untuk menyelesaikan setup.

### Instalasi dan Konfigurasi Influx CLI

1. **Download dan Instalasi Influx CLI:**
   Unduh paket Influx CLI versi terbaru dan instal menggunakan `dpkg`:
   ```bash
   # Ubuntu/Debian AMD64
   curl -LO https://dl.influxdata.com/influxdb/releases/influxdb2-client-2.7.6-linux-amd64.tar.gz
   tar xvzf influxdb2-client-2.7.6-linux-amd64.tar.gz
   sudo cp influxdb2-client-2.7.6-linux-amd64/influx /usr/local/bin/
   ```

2. **Konfigurasi CLI:**
   Konfigurasikan CLI InfluxDB untuk menggunakan token autentikasi:
   ```bash
   influx config create --config-name default --host-url http://localhost:8086 --org <YOUR_ORG_NAME> --token <YOUR_TOKEN> --active
   ```

   Gantilah `<YOUR_ORG_NAME>` dengan nama organisasi Anda dan `<YOUR_TOKEN>` dengan token yang didapatkan saat setup.

### Menambahkan Data ke InfluxDB

1. **Gunakan CLI untuk Menulis Data:**
   Berikut adalah contoh cara menulis data menggunakan CLI:
   ```bash
   influx write --bucket <YOUR_BUCKET_NAME> --org <YOUR_ORG_NAME> --precision s "mem,host=host1 used_percent=23.43234543 1556896326"
   ```

   Gantilah `<YOUR_BUCKET_NAME>` dan `<YOUR_ORG_NAME>` dengan nama bucket dan organisasi Anda.

Untuk informasi lebih lanjut dan versi terbaru, Anda bisa merujuk ke dokumentasi resmi InfluxDB di [InfluxData Documentation](https://docs.influxdata.com/influxdb/v2/install/?t=Linux) dan [Influx CLI Documentation](https://docs.influxdata.com/influxdb/v2/reference/cli/influx/?t=Linux).
