### Langkah-langkah Instalasi Prometheus di Ubuntu 22.04

1. **Update Sistem:**
   Pastikan sistem Anda diperbarui:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Buat Pengguna Prometheus:**
   Buat pengguna dan grup untuk Prometheus:
   ```bash
   sudo useradd --no-create-home --shell /bin/false prometheus
   ```

3. **Download Prometheus:**
   Unduh versi terbaru dari Prometheus dari halaman [releases di GitHub](https://github.com/prometheus/prometheus/releases). Gunakan perintah berikut untuk memastikan Anda mendapatkan versi terbaru:
   ```bash
   cd /tmp
   PROMETHEUS_VERSION=$(curl -s https://api.github.com/repos/prometheus/prometheus/releases/latest | grep tag_name | cut -d '"' -f 4)
   wget https://github.com/prometheus/prometheus/releases/download/${PROMETHEUS_VERSION}/prometheus-${PROMETHEUS_VERSION}.linux-amd64.tar.gz
   tar -xvzf prometheus-${PROMETHEUS_VERSION}.linux-amd64.tar.gz
   cd prometheus-${PROMETHEUS_VERSION}.linux-amd64
   ```

4. **Salin File Biner dan Konfigurasi:**
   Salin file biner dan file konfigurasi ke lokasi yang tepat:
   ```bash
   sudo cp prometheus /usr/local/bin/
   sudo cp promtool /usr/local/bin/
   sudo mkdir /etc/prometheus
   sudo cp prometheus.yml /etc/prometheus/
   sudo cp -r consoles /etc/prometheus/
   sudo cp -r console_libraries /etc/prometheus/
   ```

5. **Setel Kepemilikan File:**
   Ubah kepemilikan file ke pengguna `prometheus`:
   ```bash
   sudo chown -R prometheus:prometheus /etc/prometheus
   sudo chown prometheus:prometheus /usr/local/bin/prometheus
   sudo chown prometheus:prometheus /usr/local/bin/promtool
   ```

6. **Buat Direktori Data:**
   Buat direktori untuk data Prometheus dan setel kepemilikan:
   ```bash
   sudo mkdir /var/lib/prometheus
   sudo chown prometheus:prometheus /var/lib/prometheus
   ```

7. **Buat File Service:**
   Buat file service systemd untuk Prometheus:
   ```bash
   sudo nano /etc/systemd/system/prometheus.service
   ```

   Isi file dengan konfigurasi berikut:
   ```ini
   [Unit]
   Description=Prometheus
   Wants=network-online.target
   After=network-online.target

   [Service]
   User=prometheus
   Group=prometheus
   Type=simple
   ExecStart=/usr/local/bin/prometheus \
     --config.file /etc/prometheus/prometheus.yml \
     --storage.tsdb.path /var/lib/prometheus/ \
     --web.console.templates=/etc/prometheus/consoles \
     --web.console.libraries=/etc/prometheus/console_libraries

   [Install]
   WantedBy=multi-user.target
   ```

8. **Reload dan Mulai Service:**
   Reload systemd dan mulai layanan Prometheus:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start prometheus
   sudo systemctl enable prometheus
   ```

9. **Verifikasi Instalasi:**
   Verifikasi bahwa Prometheus berjalan:
   ```bash
   sudo systemctl status prometheus
   ```

### Lokasi File Konfigurasi Prometheus

File konfigurasi utama Prometheus biasanya berada di:
```
/etc/prometheus/prometheus.yml
```

### Poin-Poin Penting dari Konfigurasi Prometheus

1. **Global Configuration:**
   Anda dapat mengatur konfigurasi global di bagian `global`:
   ```yaml
   global:
     scrape_interval: 15s
     evaluation_interval: 15s
   ```

2. **Scrape Configs:**
   Di bagian `scrape_configs`, tambahkan target yang ingin Anda monitor. Contoh untuk memonitor node sendiri:
   ```yaml
   scrape_configs:
     - job_name: 'prometheus'
       static_configs:
         - targets: ['localhost:9090']
     - job_name: 'node'
       static_configs:
         - targets: ['localhost:9100']
   ```

3. **Alerting Rules:**
   Anda bisa menambahkan aturan alerting di file terpisah dan merujuknya dalam konfigurasi Prometheus:
   ```yaml
   rule_files:
     - "alert.rules"
   ```

4. **Retention Policy:**
   Anda bisa mengatur kebijakan penyimpanan data dengan menambahkan flag `--storage.tsdb.retention.time` pada `ExecStart` di file service. Contoh untuk menyimpan data selama 15 hari:
   ```ini
   ExecStart=/usr/local/bin/prometheus \
     --config.file /etc/prometheus/prometheus.yml \
     --storage.tsdb.path /var/lib/prometheus/ \
     --storage.tsdb.retention.time=15d \
     --web.console.templates=/etc/prometheus/consoles \
     --web.console.libraries=/etc/prometheus/console_libraries
   ```

Dengan mengikuti langkah-langkah di atas, Anda seharusnya bisa menginstal dan mengkonfigurasi Prometheus di Ubuntu 22.04 dengan sukses. Jika ada pertanyaan lebih lanjut, jangan ragu untuk bertanya!