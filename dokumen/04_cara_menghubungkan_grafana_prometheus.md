### Langkah-langkah Menghubungkan Grafana ke Prometheus

1. **Akses Interface Grafana:**
   Buka web browser dan akses interface Grafana di `http://<your_grafana_host>:3000`.

2. **Login ke Grafana:**
   - Gunakan kredensial default untuk login:
     - Username: `admin`
     - Password: `admin`
   - Anda akan diminta untuk mengubah password setelah login pertama kali.

3. **Tambahkan Data Source Prometheus:**
   - Setelah login, klik pada ikon gear (Configuration) di sidebar kiri.
   - Pilih "Data Sources".
   - Klik tombol "Add data source".

4. **Konfigurasi Data Source Prometheus:**
   - Pilih "Prometheus" dari daftar opsi data source.
   - Isi kolom "URL" dengan alamat Prometheus Anda, misalnya `http://localhost:9090` jika Prometheus berjalan di server yang sama dengan Grafana.
   - Sesuaikan pengaturan lainnya sesuai kebutuhan Anda (biasanya pengaturan default sudah cukup).
   - Klik "Save & Test" untuk menyimpan konfigurasi dan menguji koneksi.

### Membuat Dashboard di Grafana

1. **Buat Dashboard Baru:**
   - Klik ikon plus (+) di sidebar kiri.
   - Pilih "Dashboard" -> "New Dashboard".

2. **Tambah Panel Baru:**
   - Klik "Add new panel".

3. **Konfigurasi Panel:**
   - Pilih data source Prometheus yang telah Anda tambahkan sebelumnya.
   - Masukkan query Prometheus untuk data yang ingin Anda tampilkan. Berikut adalah beberapa contoh query:

     - **CPU Usage:**
       ```promql
       100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
       ```

     - **Memory Usage:**
       ```promql
       node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100
       ```

     - **Disk Usage:**
       ```promql
       node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"} * 100
       ```

4. **Visualisasi Data:**
   - Setelah memasukkan query, Grafana akan menampilkan pratinjau data di panel.
   - Anda bisa memilih tipe visualisasi yang berbeda (graph, gauge, bar chart, dll.) dari opsi yang tersedia di bagian "Visualization".

5. **Simpan Panel:**
   - Setelah konfigurasi selesai, klik "Apply" untuk menyimpan panel.
   - Anda bisa menambahkan beberapa panel ke dalam satu dashboard dengan mengulangi langkah-langkah di atas.

6. **Simpan Dashboard:**
   - Klik ikon disk di bagian atas kanan untuk menyimpan dashboard.
   - Beri nama dashboard dan simpan.

### Contoh Query dan Visualisasi di Grafana

1. **CPU Usage:**
   - Query: 
     ```promql
     100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
     ```
   - Visualization: Line Graph.

2. **Memory Usage:**
   - Query: 
     ```promql
     node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100
     ```
   - Visualization: Gauge.

3. **Disk Usage:**
   - Query: 
     ```promql
     node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"} * 100
     ```
   - Visualization: Bar Chart.

### Mengimpor Template Dashboard dari Grafana

1. **Dapatkan Dashboard ID atau URL:**
   - Buka halaman template dashboard untuk Node Exporter di [Grafana Dashboard](https://grafana.com/grafana/dashboards/1860-node-exporter-full/).
   - Catat ID dashboard, yaitu `1860`.

2. **Import Dashboard di Grafana:**
   - Klik pada ikon plus (+) di sidebar kiri.
   - Pilih "Import".

3. **Masukkan Dashboard ID:**
   - Di kolom "Import via grafana.com", masukkan ID `1860`.
   - Klik "Load".

4. **Konfigurasi Import:**
   - Pada halaman konfigurasi import, pilih data source Prometheus yang telah Anda tambahkan sebelumnya dari dropdown menu.
   - Klik "Import".

### Menyimpan dan Mengelola Dashboard

1. **Simpan Dashboard:**
   - Setelah selesai menambahkan atau mengedit panel, klik ikon disk (Save) di bagian atas untuk menyimpan dashboard.
   - Beri nama dashboard dan klik "Save".

2. **Mengelola Dashboard:**
   - Anda bisa mengedit, menyalin, atau menghapus panel dan dashboard kapan saja.
   - Pergi ke menu "Dashboards" di sidebar kiri untuk melihat daftar semua dashboard yang telah Anda buat.