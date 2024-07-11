Berikut adalah tutorial yang telah diperbaiki untuk mengatur alerting di Grafana menggunakan data dari `node_exporter`, serta pengiriman notifikasi ke email dan Slack, termasuk cara mengetest konfigurasi alert.

### Langkah-langkah Konfigurasi Alerting di Grafana

#### Konfigurasi Notifikasi Channel

1. **Tambahkan Email Notification Channel:**
   - Klik ikon gear (Configuration) di sidebar kiri.
   - Pilih "Notification channels".
   - Klik tombol "Add channel".

2. **Konfigurasi Email Channel:**
   - Isi kolom "Name" dengan nama channel (misalnya, `Email Notifications`).
   - Pilih "Email" sebagai type.
   - Isi kolom "Email addresses" dengan alamat email tujuan (misalnya, `youremail@example.com`).
   - Klik "Save".

3. **Tambahkan Slack Notification Channel melalui Automations:**
   - Buka Slack dan pergi ke menu "Automations" -> "Apps".
   - Tambah aplikasi "Incoming Webhooks".
   - Klik "Add to Slack" dan pilih channel tujuan untuk notifikasi.
   - Salin URL Webhook yang diberikan oleh Slack.

4. **Konfigurasi Slack Channel di Grafana:**
   - Kembali ke Grafana dan tambahkan notification channel baru.
   - Isi kolom "Name" dengan nama channel (misalnya, `Slack Notifications`).
   - Pilih "Slack" sebagai type.
   - Isi kolom "Url" dengan Incoming Webhook URL yang telah Anda salin dari Slack.
   - Klik "Save".

### Konfigurasi SMTP di Grafana untuk Pengiriman Email

1. **Edit File Konfigurasi Grafana:**
   - Buka file konfigurasi Grafana (biasanya `grafana.ini` atau `custom.ini` di `/etc/grafana/`).
   - Cari bagian `[smtp]` dan tambahkan atau edit konfigurasi berikut:
     ```ini
     [smtp]
     enabled = true
     host = smtp.example.com:587
     user = your_user
     password = your_password
     from_address = grafana@example.com
     from_name = Grafana
     ```

2. **Restart Layanan Grafana:**
   - Setelah mengedit file konfigurasi, restart layanan Grafana:
     ```bash
     sudo systemctl restart grafana-server
     ```

### Membuat Alert Rule

1. **Buat atau Gunakan Dashboard yang Ada:**
   - Pergi ke dashboard yang sudah ada atau buat dashboard baru.
   - Jika membuat dashboard baru, tambahkan panel baru dengan klik "Add new panel".

2. **Konfigurasi Panel untuk Alerting:**
   - Di bagian "Metrics", pilih data source Prometheus yang telah Anda tambahkan sebelumnya.
   - Masukkan query Prometheus untuk data yang ingin Anda monitor. Contoh query untuk CPU Usage:
     ```promql
     100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
     ```

3. **Atur Alert:**
   - Di bagian atas, klik tab "Alert".
   - Klik tombol "Create Alert".
   - Konfigurasi rule alert. Misalnya:
     - **Name**: `High CPU Usage`
     - **Evaluate every**: `1m`
     - **For**: `5m`
     - **Conditions**: 
       - Klik "Add condition".
       - Pilih `avg()` dari dropdown dan masukkan query CPU Usage di atas.
       - Set threshold condition. Misalnya, `IS ABOVE 80`.
   - Di bagian "Notifications", tambahkan notification channel yang telah Anda buat (Email dan Slack).
   - Klik "Save".

### Menguji Konfigurasi Alert

1. **Uji Email Notification:**
   - Kembali ke "Notification channels".
   - Klik pada channel email yang telah Anda buat.
   - Klik tombol "Send Test".

2. **Uji Slack Notification:**
   - Kembali ke "Notification channels".
   - Klik pada channel Slack yang telah Anda buat.
   - Klik tombol "Send Test".

### Contoh Penerapan Alerting

1. **Contoh Alert untuk CPU Usage Tinggi:**
   - Query: 
     ```promql
     100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
     ```
   - Condition: 
     - IS ABOVE 80
   - Notification Channels: 
     - Email Notifications
     - Slack Notifications