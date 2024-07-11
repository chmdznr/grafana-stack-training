### Langkah-langkah Instalasi Node Exporter di Ubuntu 22.04

1. **Update Sistem:**
   Pastikan sistem Anda diperbarui:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Download Node Exporter:**
   Unduh versi terbaru dari Node Exporter dari halaman [releases di GitHub](https://github.com/prometheus/node_exporter/releases). Gunakan perintah berikut untuk memastikan Anda mendapatkan versi terbaru:
   ```bash
   cd /tmp
   NODE_EXPORTER_VERSION=$(curl -s https://api.github.com/repos/prometheus/node_exporter/releases/latest | grep tag_name | cut -d '"' -f 4)
   wget https://github.com/prometheus/node_exporter/releases/download/${NODE_EXPORTER_VERSION}/node_exporter-${NODE_EXPORTER_VERSION}.linux-amd64.tar.gz
   tar -xvzf node_exporter-${NODE_EXPORTER_VERSION}.linux-amd64.tar.gz
   cd node_exporter-${NODE_EXPORTER_VERSION}.linux-amd64
   ```

3. **Salin File Biner:**
   Salin file biner ke lokasi yang tepat:
   ```bash
   sudo cp node_exporter /usr/local/bin/
   ```

4. **Buat Pengguna Node Exporter:**
   Buat pengguna dan grup untuk Node Exporter:
   ```bash
   sudo useradd --no-create-home --shell /bin/false node_exporter
   sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
   ```

5. **Buat File Service:**
   Buat file service systemd untuk Node Exporter:
   ```bash
   sudo nano /etc/systemd/system/node_exporter.service
   ```

   Isi file dengan konfigurasi berikut:
   ```ini
   [Unit]
   Description=Node Exporter
   Wants=network-online.target
   After=network-online.target

   [Service]
   User=node_exporter
   Group=node_exporter
   Type=simple
   ExecStart=/usr/local/bin/node_exporter

   [Install]
   WantedBy=multi-user.target
   ```

6. **Reload dan Mulai Service:**
   Reload systemd dan mulai layanan Node Exporter:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start node_exporter
   sudo systemctl enable node_exporter
   ```

7. **Verifikasi Instalasi:**
   Verifikasi bahwa Node Exporter berjalan:
   ```bash
   sudo systemctl status node_exporter
   ```

### Konfigurasi Prometheus untuk Mengumpulkan Data dari Node Exporter

1. **Edit File Konfigurasi Prometheus:**
   Tambahkan konfigurasi scrape untuk Node Exporter di file `prometheus.yml`:
   ```yaml
   scrape_configs:
     - job_name: 'node_exporter'
       static_configs:
         - targets: ['localhost:9100']
   ```

2. **Restart Layanan Prometheus:**
   Setelah mengedit file konfigurasi, restart layanan Prometheus agar perubahan diterapkan:
   ```bash
   sudo systemctl restart prometheus
   ```

### Contoh Penggunaan Data Node Exporter di Prometheus

1. **Akses Interface Prometheus:**
   Buka web browser dan akses interface Prometheus di `http://<your_prometheus_host>:9090`.

2. **Contoh Query Prometheus:**
   Gunakan query berikut untuk mengakses data dari Node Exporter:

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

3. **Menjalankan Query di Prometheus:**
   - Buka tab "Graph" di interface Prometheus.
   - Masukkan salah satu query di atas ke dalam kotak input query.
   - Klik tombol "Execute" untuk menjalankan query dan melihat hasilnya dalam bentuk grafis.