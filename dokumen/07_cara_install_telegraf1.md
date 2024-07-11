### Langkah-langkah Instalasi Telegraf di Ubuntu 22.04

1. **Update Sistem:**
   Pastikan sistem Anda diperbarui:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Tambahkan Repository Telegraf:**
   Tambahkan kunci GPG dan repository Telegraf:
   ```bash
   curl -s https://repos.influxdata.com/influxdata-archive.key > influxdata-archive.key
   echo '943666881a1b8d9b849b74caebf02d3465d6beb716510d86a39f6c8e8dac7515 influxdata-archive.key' | sha256sum -c && cat influxdata-archive.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/influxdata-archive.gpg > /dev/null
   echo 'deb [signed-by=/etc/apt/trusted.gpg.d/influxdata-archive.gpg] https://repos.influxdata.com/debian stable main' | sudo tee /etc/apt/sources.list.d/influxdata.list
   ```

3. **Instalasi Telegraf:**
   Instal Telegraf:
   ```bash
   sudo apt-get update && sudo apt-get install telegraf
   ```

4. **Mulai dan Aktifkan Layanan Telegraf:**
   Mulai layanan Telegraf dan atur agar berjalan otomatis saat sistem dinyalakan:
   ```bash
   sudo systemctl start telegraf
   sudo systemctl enable telegraf
   ```

### Konfigurasi Telegraf

1. **Edit File Konfigurasi Telegraf:**
   Buka file konfigurasi Telegraf untuk diedit:
   ```bash
   sudo nano /etc/telegraf/telegraf.conf
   ```

2. **Konfigurasi Outputs:**
   Konfigurasi output untuk mengirim data ke InfluxDB:
   ```toml
   [[outputs.influxdb_v2]]
     urls = ["http://localhost:8086"]
     token = "YOUR_INFLUXDB_TOKEN"
     organization = "YOUR_ORG_NAME"
     bucket = "YOUR_BUCKET_NAME"
   ```

3. **Konfigurasi Inputs:**
   Tambahkan konfigurasi berikut untuk mengumpulkan metrik yang setara dengan `node_exporter`:
   ```toml
   # Read metrics about cpu usage
   [[inputs.cpu]]
     percpu = true
     totalcpu = true
     fielddrop = ["time_*"]

   # Read metrics about disk usage by mount point
   [[inputs.disk]]
     ignore_fs = ["tmpfs", "devtmpfs"]

   # Read metrics about disk IO by device
   [[inputs.diskio]]
     # no configuration

   # Get kernel statistics from /proc/stat
   [[inputs.kernel]]
     # no configuration

   # Read metrics about memory usage
   [[inputs.mem]]
     # no configuration

   # Get the number of processes and group them by status
   [[inputs.processes]]
     # no configuration

   # Read metrics about swap memory usage
   [[inputs.swap]]
     # no configuration

   # Read metrics about system load & uptime
   [[inputs.system]]
     # no configuration

   # Read metrics about network interface usage
   [[inputs.net]]
     # collect data only about specific interfaces
     # interfaces = ["eth0"]

   [[inputs.netstat]]
     # no configuration

   [[inputs.interrupts]]
     # no configuration

   [[inputs.linux_sysctl_fs]]
     # no configuration
   ```

### Menggunakan Data di InfluxDB Melalui Web UI

1. **Akses InfluxDB Web UI:**
   Buka web browser dan akses InfluxDB Web UI di `http://localhost:8086`.

2. **Login:**
   Masuk dengan menggunakan username dan password yang telah Anda buat selama setup awal InfluxDB.

3. **Navigasi ke Data Explorer:**
   Setelah login, klik pada menu "Data Explorer" di sidebar kiri.

4. **Pilih Bucket:**
   Pilih bucket yang sesuai di dropdown "Bucket". Ini adalah bucket yang telah Anda konfigurasikan di output Telegraf (`YOUR_BUCKET_NAME`).

5. **Eksplorasi Data:**
   - Di bagian "Filter" pada Data Explorer, pilih "Measurement" untuk memilih jenis metrik yang ingin Anda lihat (misalnya, `cpu`, `mem`, `disk`, dll.).
   - Setelah memilih measurement, Anda dapat memilih field dan tag yang ingin Anda tampilkan.
   - Klik "Submit" untuk menampilkan data dalam bentuk tabel atau grafis.

6. **Membuat Dashboard:**
   - Klik pada menu "Dashboards" di sidebar kiri.
   - Klik tombol "Create Dashboard" untuk membuat dashboard baru.
   - Tambahkan sel (cell) ke dashboard dengan memilih data yang ingin Anda visualisasikan dan konfigurasi jenis visualisasi (grafik garis, bar, gauge, dll.).

### Menggunakan Data di Grafana

1. **Tambahkan Data Source InfluxDB di Grafana:**
   - Buka web interface Grafana di `http://<your_grafana_host>:3000`.
   - Login dengan kredensial admin.
   - Pergi ke menu "Configuration" -> "Data Sources".
   - Klik "Add data source" dan pilih "InfluxDB".

2. **Konfigurasi Data Source InfluxDB di Grafana:**
   - **Query Language**: `InfluxQL`
   - **URL**: `http://localhost:8086`.
   - **Custom HTTP Headers**: 
     - Header: `Authorization`
     - Value: `Token my-super-secret-auth-token ` (perhatikan kapital "T" dan spasi sebelum dan sesudah nilai token)
   - **Database**: Nama bucket Anda.
   - **HTTP Method**: Pilih POST.
   - Klik "Save & Test" untuk menguji koneksi.

3. **Import Dashboard Telegraf System di Grafana:**
   - Pergi ke menu "Dashboards" -> "Manage".
   - Klik "Import".
   - Masukkan `928` pada kolom `Grafana.com Dashboard`.
   - Klik "Load".
   - Pilih data source InfluxDB yang telah Anda tambahkan.
   - Klik "Import" untuk menyelesaikan proses.
   
**Catatan Koneksi InfluxDB dengan mode Query Language `Flux`**
Jika Anda menggunakan dashboard yang membutuhkan koneksi ke InfluxDB dengan mode query `Flux`, maka konfigurasi Data Source InfluxDB menjadi seperti ini:
- **Query Language**: `Flux`
- **URL**: `http://localhost:8086`.
- **Auth**: pilih `Basic auth`
- **Basic Auth Details**: isi dengan `User` dan `Password` InfluxDB Anda
- **Organization**: isi dengan nama organisasi yang sudah dikonfigurasi di InfluxDB, contoh: `my-org`
- **Token**: isi dengan API token yang sudah dikonfigurasi di InfluxDB, contoh: `my-super-secret-auth-token`.
- **Default Bucket**: Nama bucket Anda.
- Klik "Save & Test" untuk menguji koneksi.

### Contoh Query untuk Metrik dari Telegraf

1. **CPU Usage:**
   - Query:
     ```flux
     from(bucket: "YOUR_BUCKET_NAME")
       |> range(start: -1h)
       |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_user" and r.cpu == "cpu-total")
     ```
   - Visualization: Line Graph.

2. **Memory Usage:**
   - Query:
     ```flux
     from(bucket: "YOUR_BUCKET_NAME")
       |> range(start: -1h)
       |> filter(fn: (r) => r._measurement == "mem" and r._field == "used_percent")
     ```
   - Visualization: Gauge.

3. **Disk Usage:**
   - Query:
     ```flux
     from(bucket: "YOUR_BUCKET_NAME")
       |> range(start: -1h)
       |> filter(fn: (r) => r._measurement == "disk" and r._field == "used_percent")
     ```
   - Visualization: Bar Chart.

4. **Network Traffic:**
   - Query:
     ```flux
     from(bucket: "YOUR_BUCKET_NAME")
       |> range(start: -1h)
       |> filter(fn: (r) => r._measurement == "net" and r._field == "bytes_recv" and r.interface == "eth0")
     ```
   - Visualization: Line Graph.

5. **System Load:**
   - Query:
     ```flux
     from(bucket: "YOUR_BUCKET_NAME")
       |> range(start: -1h)
       |> filter(fn: (r) => r._measurement == "system" and r._field == "load1")
     ```
   - Visualization: Line Graph.

6. **Swap Usage:**
   - Query:
     ```flux
     from(bucket: "YOUR_BUCKET_NAME")
       |> range(start: -1h)
       |> filter(fn: (r) => r._measurement == "swap" and r._field == "used_percent")
     ```
   - Visualization: Gauge.


### Referensi Tambahan

- [Instalasi Telegraf dari Dokumentasi Resmi](https://docs.influxdata.com/telegraf/v1/install/?t=Ubuntu+20.04+LTS+and+newer)
- [Dashboard Telegraf System di Grafana](https://grafana.com/grafana/dashboards/928)