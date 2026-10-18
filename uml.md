# UML Documentation — AI NOC (Network Operations Center)

## Daftar Diagram

| # | Diagram | Jenis UML | Keterangan |
|---|---------|-----------|------------|
| 1 | [Use Case Diagram](#1-use-case-diagram) | Behavioral | Siapa melakukan apa dalam sistem |
| 2 | [Class Diagram (Database)](#2-class-diagram-database-model) | Structural | Struktur tabel dan relasi di MySQL |
| 3 | [Sequence Diagram — Monitoring](#3-sequence-diagram--alur-monitoring-data) | Behavioral | Alur data dari Agent → MySQL |
| 4 | [Sequence Diagram — Anomaly Detection](#4-sequence-diagram--anomaly-detection-pipeline) | Behavioral | Alur AI NOC mendeteksi anomali |
| 5 | [Sequence Diagram — Event System](#5-sequence-diagram--event-system-shutdownrestartsleep) | Behavioral | Alur deteksi shutdown/restart/sleep |
| 6 | [Activity Diagram — Pipeline ML](#6-activity-diagram--ml-pipeline) | Behavioral | Flowchart proses machine learning |
| 7 | [Activity Diagram — Buffer Sync](#7-activity-diagram--buffer-sync-offline-resilience) | Behavioral | Flowchart mekanisme buffer offline |
| 8 | [Component Diagram](#8-component-diagram) | Structural | Arsitektur komponen sistem |
| 9 | [Deployment Diagram](#9-deployment-diagram) | Structural | Infrastruktur fisik dan container |
| 10 | [State Diagram — Device](#10-state-diagram--device-lifecycle) | Behavioral | Status device (online/offline/anomaly) |

---

## 1. Use Case Diagram

Menunjukkan aktor (pengguna/sistem) dan interaksi mereka dengan sistem AI NOC.

### Agent Use Cases

```mermaid
graph LR
    AGENT["🖥️ Agent<br/>(PC Client)"]

    AGENT --> UC1(["Registrasi Device"])
    AGENT --> UC2(["Kirim Heartbeat<br/>CPU, RAM, Disk"])
    AGENT --> UC3(["Kirim Data Network"])
    AGENT --> UC4(["Kirim Data Process"])
    AGENT --> UC5(["Kirim Data Aplikasi Aktif"])
    AGENT --> UC6(["Kirim Event<br/>Startup/Shutdown/Restart"])
    AGENT --> UC7(["Deteksi Sleep dan Lockscreen"])
    AGENT --> UC8(["Buffer Data saat API Offline"])
    AGENT --> UC9(["Sync Buffer saat API Online"])

    style AGENT fill:#e74c3c,stroke:#2c3e50,color:#fff
    style UC1 fill:#fadbd8,stroke:#e74c3c,color:#2c3e50
    style UC2 fill:#fadbd8,stroke:#e74c3c,color:#2c3e50
    style UC3 fill:#fadbd8,stroke:#e74c3c,color:#2c3e50
    style UC4 fill:#fadbd8,stroke:#e74c3c,color:#2c3e50
    style UC5 fill:#fadbd8,stroke:#e74c3c,color:#2c3e50
    style UC6 fill:#fadbd8,stroke:#e74c3c,color:#2c3e50
    style UC7 fill:#fadbd8,stroke:#e74c3c,color:#2c3e50
    style UC8 fill:#fadbd8,stroke:#e74c3c,color:#2c3e50
    style UC9 fill:#fadbd8,stroke:#e74c3c,color:#2c3e50
```

### Administrator Use Cases

```mermaid
graph LR
    ADMIN["👤 Administrator"]

    ADMIN --> UC10(["Login Dashboard"])
    ADMIN --> UC11(["Monitor Status Device"])
    ADMIN --> UC12(["Lihat Data Heartbeat"])
    ADMIN --> UC13(["Lihat Aplikasi Aktif"])
    ADMIN --> UC14(["Lihat Event Device"])
    ADMIN --> UC15(["Lihat Alert Anomaly"])
    ADMIN --> UC16(["Kelola IP Whitelist"])
    ADMIN --> UC17(["Kelola Database via phpMyAdmin"])

    style ADMIN fill:#3498db,stroke:#2c3e50,color:#fff
    style UC10 fill:#d6eaf8,stroke:#3498db,color:#2c3e50
    style UC11 fill:#d6eaf8,stroke:#3498db,color:#2c3e50
    style UC12 fill:#d6eaf8,stroke:#3498db,color:#2c3e50
    style UC13 fill:#d6eaf8,stroke:#3498db,color:#2c3e50
    style UC14 fill:#d6eaf8,stroke:#3498db,color:#2c3e50
    style UC15 fill:#d6eaf8,stroke:#3498db,color:#2c3e50
    style UC16 fill:#d6eaf8,stroke:#3498db,color:#2c3e50
    style UC17 fill:#d6eaf8,stroke:#3498db,color:#2c3e50
```

### AI NOC Use Cases

```mermaid
graph LR
    AINOC["🤖 AI NOC Service"]
    SCHEDULER["⏰ Scheduler"]

    AINOC --> UC18(["Baca Data Heartbeat dari MySQL"])
    AINOC --> UC19(["Preprocessing dan Feature Engineering"])
    AINOC --> UC20(["Prediksi Anomaly - Isolation Forest"])
    AINOC --> UC21(["Evaluasi Severity - Critical/Warning"])
    AINOC --> UC22(["Simpan Alert ke MySQL"])
    AINOC --> UC23(["Kirim Notifikasi Telegram"])
    AINOC --> UC24(["Retrain Model Berkala"])
    SCHEDULER --> UC24

    style AINOC fill:#9b59b6,stroke:#2c3e50,color:#fff
    style SCHEDULER fill:#f39c12,stroke:#2c3e50,color:#fff
    style UC18 fill:#e8daef,stroke:#9b59b6,color:#2c3e50
    style UC19 fill:#e8daef,stroke:#9b59b6,color:#2c3e50
    style UC20 fill:#e8daef,stroke:#9b59b6,color:#2c3e50
    style UC21 fill:#e8daef,stroke:#9b59b6,color:#2c3e50
    style UC22 fill:#e8daef,stroke:#9b59b6,color:#2c3e50
    style UC23 fill:#e8daef,stroke:#9b59b6,color:#2c3e50
    style UC24 fill:#fdebd0,stroke:#f39c12,color:#2c3e50
```
### Penjelasan Aktor

| Aktor | Jenis | Deskripsi |
|-------|-------|-----------|
| **Administrator** | Manusia | Staff IT yang memantau kesehatan infrastruktur via dashboard |
| **Agent** | Sistem | Software monitoring yang berjalan di setiap PC client |
| **AI NOC Service** | Sistem | Service ML yang mendeteksi anomali secara otomatis |
| **Scheduler** | Sistem | APScheduler yang menjalankan pipeline secara berkala |

---

## 2. Class Diagram (Database Model)

Menunjukkan struktur tabel di MySQL dan relasi antar entitas.

```mermaid
classDiagram
    class Device {
        +int id PK
        +string device_id UK
        +string hostname
        +string ip_address
        +datetime registered_at
        +datetime last_seen
    }

    class Heartbeat {
        +int id PK
        +string device_id FK
        +float cpu_percent
        +float ram_percent
        +float disk_percent
        +string status
        +datetime created_at
    }

    class NetworkLog {
        +int id PK
        +string device_id FK
        +bigint upload_bytes
        +bigint download_bytes
        +float latency_ms
        +datetime created_at
    }

    class Process {
        +int id PK
        +string device_id FK
        +int pid
        +string name
        +float cpu_percent
        +float ram_mb
        +datetime created_at
    }

    class Application {
        +int id PK
        +string device_id FK
        +string app_name
        +text window_title
        +datetime created_at
    }

    class ActiveApplication {
        +int id PK
        +string device_id FK
        +string app_name
        +text window_title
        +bool is_browser
        +text browser_tab_title
        +datetime updated_at
    }

    class Event {
        +int id PK
        +string device_id FK
        +string event_type
        +datetime timestamp
        +datetime created_at
    }

    class Alert {
        +int id PK
        +string device_id FK
        +string alert_type
        +string severity
        +float anomaly_score
        +text description
        +text features
        +bool is_resolved
        +datetime detected_at
        +datetime resolved_at
        +datetime created_at
    }

    class IPWhitelist {
        +int id PK
        +string ip_prefix UK
        +string description
        +bool is_active
        +datetime created_at
    }

    Device --> Heartbeat : device_id
    Device --> NetworkLog : device_id
    Device --> Process : device_id
    Device --> Application : device_id
    Device --> ActiveApplication : device_id
    Device --> Event : device_id
    Device --> Alert : device_id
```

### Relasi Database (Foreign Key)

Semua tabel yang memiliki kolom `device_id` terhubung ke tabel `devices` melalui **FOREIGN KEY constraint** dengan aturan `ON DELETE CASCADE ON UPDATE CASCADE`:

| Tabel | FK Constraint | Referensi |
|-------|--------------|-----------|
| `heartbeats` | `fk_heartbeats_device` | `devices(device_id)` |
| `network_logs` | `fk_network_logs_device` | `devices(device_id)` |
| `processes` | `fk_processes_device` | `devices(device_id)` |
| `applications` | `fk_applications_device` | `devices(device_id)` |
| `active_applications` | `fk_active_apps_device` | `devices(device_id)` |
| `events` | `fk_events_device` | `devices(device_id)` |
| `alerts` | `fk_alerts_device` | `devices(device_id)` |

**Aturan CASCADE:**
- `ON DELETE CASCADE` — Jika device dihapus, semua data terkait (heartbeat, log, dll) otomatis ikut terhapus
- `ON UPDATE CASCADE` — Jika `device_id` diubah, semua referensi di tabel lain otomatis terupdate

### Penjelasan Tabel

| Tabel | Volume Data | Keterangan |
|-------|-------------|------------|
| `devices` | 1 per PC | Registrasi device yang di-monitor |
| `heartbeats` | ~1.440/hari/device | Snapshot CPU, RAM, Disk setiap 60 detik |
| `network_logs` | ~2.880/hari/device | Traffic upload/download dan latency setiap 30 detik |
| `processes` | ~288/hari/device | Top proses yang berjalan setiap 5 menit |
| `applications` | ~17.280/hari/device | Riwayat aplikasi yang dibuka setiap 5 detik |
| `active_applications` | Real-time (delta sync) | Aplikasi yang sedang aktif saat ini, sync setiap 5 detik |
| `events` | Event-driven | Startup, shutdown, restart, lockscreen, sleep |
| `alerts` | Anomaly-driven | Hasil deteksi anomali dari AI NOC |
| `ip_whitelist` | Konfigurasi | Daftar IP prefix yang diizinkan akses API |

**Perhitungan Volume Data per hari per device:**

- `heartbeats` — Dikirim setiap **60 detik** (1 menit) = **1 data/menit**
  → 1 × 60 menit × 24 jam = **1.440 data/hari**

- `network_logs` — Dikirim setiap **30 detik** = **2 data/menit**
  → 2 × 60 menit × 24 jam = **2.880 data/hari**

- `processes` — Dikirim setiap **5 menit** = **1 data/5 menit**
  → 12 per jam × 24 jam = **288 data/hari**

- `applications` — Dikirim setiap **5 detik** = **12 data/menit**
  → 12 × 60 menit × 24 jam = **17.280 data/hari**

> Semua interval bisa dikonfigurasi di file `.env` agent: `HEARTBEAT_INTERVAL`, `NETWORK_INTERVAL`, `PROCESS_INTERVAL`, `APPLICATION_INTERVAL` (dalam satuan detik).

### Tipe Event

| event_type | Trigger |
|------------|---------|
| `startup` | PC dinyalakan / agent mulai |
| `shutdown` | PC dimatikan |
| `restart` | PC di-restart (deteksi via Windows Event Log) |
| `lockscreen` | Layar dikunci (Win+L) |
| `sleep` | PC masuk mode sleep/hibernate |

---

## 3. Sequence Diagram — Alur Monitoring Data

Menunjukkan alur data dari Agent mengumpulkan data hingga tersimpan di MySQL.

```mermaid
sequenceDiagram
    participant PC as Agent (PC Client)
    participant API as FastAPI
    participant MW as IP Middleware
    participant RD as Redis Queue
    participant RQ as RQ Worker
    participant DB as MySQL
    participant BUF as SQLite Buffer

    Note over PC: Setiap 60 detik
    PC->>PC: Collect CPU, RAM, Disk
    PC->>API: POST /api/v1/heartbeat<br/>{device_id, cpu, ram, disk}
    
    API->>MW: Validasi IP (X-Real-IP header)
    
    alt IP Valid (dalam whitelist)
        MW-->>API: Allowed
        API->>RD: Enqueue ke queue "telemetry"
        RD-->>API: Job ID
        API-->>PC: 200 OK
        
        RQ->>RD: Dequeue job
        RQ->>DB: INSERT INTO heartbeats
        DB-->>RQ: Saved
    else IP Tidak Valid
        MW-->>API: 403 Forbidden
        API-->>PC: 403 Forbidden
        PC->>BUF: Simpan ke SQLite buffer
        Note over BUF: Data disimpan offline
        
        loop Retry setiap 30 detik
            PC->>API: POST /api/v1/heartbeat (dari buffer)
            alt API Online dan IP Valid
                API-->>PC: 200 OK
                PC->>BUF: Hapus data yang berhasil sync
            else API masih gagal
                PC->>BUF: Data tetap di buffer
            end
        end
    end
```

### Penjelasan Alur

1. **Agent** mengumpulkan data sistem (CPU, RAM, Disk) setiap 60 detik
2. **Agent** mengirim data ke FastAPI via HTTP POST dengan header `X-Real-IP`
3. **IP Middleware** memvalidasi IP pengirim terhadap tabel `ip_whitelist`
4. **Device Registration** menggunakan 5 skenario:
   - Scenario 1: device_id + IP cocok → update `last_seen`
   - Scenario 2: device_id tidak ada, IP ada → replace device lama (by IP)
   - Scenario 3: device_id ada, IP beda → kirim `ip_changed`, agent regenerate
   - Scenario 4: device_id + IP + hostname baru → INSERT device baru
   - Scenario 5: device_id + IP baru, tapi hostname ada → **auto-replace** device lama (by hostname)
4. Jika valid → data di-enqueue ke **Redis Queue** (queue: `telemetry`)
5. **RQ Worker** (telemetry) mengambil job dan menyimpan ke **MySQL**
6. Jika gagal (API offline/403) → data disimpan ke **SQLite buffer** lokal
7. Buffer otomatis sync saat API kembali online

---

## 4. Sequence Diagram — Anomaly Detection Pipeline

Menunjukkan alur AI NOC mendeteksi anomali dan mengirim alert.

```mermaid
sequenceDiagram
    participant SC as Scheduler (5 menit)
    participant PL as Pipeline
    participant RD as Reader
    participant DB as MySQL
    participant PP as Preprocessor
    participant ML as Isolation Forest Model
    participant EV as Evaluator
    participant WR as Writer
    participant TG as Telegram

    SC->>PL: Trigger pipeline

    PL->>RD: read_monitoring_data()
    RD->>DB: SELECT heartbeats<br/>WHERE created_at >= NOW() - 1h
    DB-->>RD: Raw data (N rows)
    RD-->>PL: DataFrame

    PL->>PP: preprocess_data(df)
    Note over PP: Agregasi per device:<br/>cpu_mean, cpu_max,<br/>ram_mean, ram_max,<br/>disk_latest
    PP-->>PL: Feature DataFrame

    PL->>ML: predict_anomalies(df)
    
    alt Model file ada
        ML->>ML: Load anomaly_model.pkl
    else Model file tidak ada
        ML->>ML: Training baru (Isolation Forest)
        ML->>ML: Simpan anomaly_model.pkl
    end
    
    Note over ML: Cek retrain berkala
    alt Sudah waktunya retrain
        ML->>RD: read_training_data()
        RD->>DB: SELECT heartbeats<br/>WHERE created_at >= NOW() - 168h
        DB-->>RD: Training data (7 hari)
        ML->>ML: Retrain model
        ML->>ML: Overwrite anomaly_model.pkl
    end
    
    ML->>ML: predict() dan decision_function()
    ML-->>PL: Predictions DataFrame

    PL->>EV: evaluate(df)
    Note over EV: Tentukan severity<br/>berdasarkan metrik aktual
    EV-->>PL: Evaluated DataFrame

    PL->>WR: write_alerts(df)
    WR->>DB: INSERT INTO alerts (hanya anomaly)
    DB-->>WR: Saved

    alt Ada anomaly critical/warning
        PL->>TG: send_alert()
        TG-->>PL: Sent
        Note over TG: Notifikasi ke Telegram Chat
    else Semua normal
        Note over PL: No notification sent
    end
```

### Penjelasan Alur Anomaly Detection

1. **Scheduler** memicu pipeline setiap 5 menit (konfigurasi `ANALYSIS_INTERVAL_MINUTES`)
2. **Reader** membaca data heartbeat dari MySQL dalam 1 jam terakhir (`DATA_WINDOW_HOURS`)
3. **Preprocessor** memiliki 2 mode:
   - `preprocess_data()` → untuk **prediksi**: agregasi per device (1 row per device)
   - `preprocess_training_data()` → untuk **training**: agregasi per device + per jam (time-window 1 jam), menghasilkan ~168 sample dari 7 hari data
4. **Model** memuat file `anomaly_model.pkl` dari Docker Volume. Jika file tidak ada, model baru akan di-training otomatis menggunakan `preprocess_training_data()`
5. **Retrain Check** — Model mengecek apakah sudah waktunya retrain (berdasarkan `RETRAIN_INTERVAL_MINUTES`). Jika ya dan data cukup (>= `RETRAIN_MIN_SAMPLES`), model di-retrain dengan data 7 hari terakhir
6. **Predict** — `predict()` menghasilkan 1 (normal) atau -1 (anomaly). `decision_function()` menghasilkan `raw_score` yang dinormalisasi ke `anomaly_score` (0-1)
7. **Evaluator** menentukan severity (critical/warning) berdasarkan nilai CPU, RAM, dan Disk
8. **Writer** menyimpan alert ke tabel `alerts` di MySQL (hanya yang anomaly)
9. **Telegram** mengirim notifikasi jika ada anomaly critical atau warning

### Data Training Isolation Forest

Data yang digunakan untuk melatih model berasal dari tabel `heartbeats` di MySQL (7 hari terakhir), kemudian diproses menjadi **5 fitur** per time-window (1 jam):

| Fitur | Sumber | Arti | Contoh Normal | Contoh Anomali |
|-------|--------|------|:-------------:|:--------------:|
| `cpu_mean` | `AVG(cpu_percent)` per jam | Rata-rata penggunaan CPU dalam 1 jam | 10-40% | >85% |
| `cpu_max` | `MAX(cpu_percent)` per jam | Penggunaan CPU tertinggi dalam 1 jam | 20-60% | >90% |
| `ram_mean` | `AVG(ram_percent)` per jam | Rata-rata penggunaan RAM dalam 1 jam | 40-70% | >85% |
| `ram_max` | `MAX(ram_percent)` per jam | Penggunaan RAM tertinggi dalam 1 jam | 50-80% | >90% |
| `disk_latest` | `LAST(disk_percent)` per jam | Penggunaan disk terakhir dalam 1 jam | 50-70% | >90% |

**Contoh data training (168 sample dari 7 hari × 24 jam):**

| Jam | cpu_mean | cpu_max | ram_mean | ram_max | disk_latest | Hasil Model |
|-----|:--------:|:-------:|:--------:|:-------:|:-----------:|:-----------:|
| 08:00 | 12.5 | 25.0 | 55.3 | 62.1 | 65.0 | Normal |
| 09:00 | 35.2 | 72.0 | 68.4 | 75.5 | 65.0 | Normal |
| 14:00 | 88.7 | 99.0 | 92.1 | 98.0 | 94.0 | **Anomali** |

> Model belajar bahwa **pola normal** adalah CPU 10-50%, RAM 40-70%, Disk 50-70%. Ketika nilai menyimpang jauh (seperti Disk 94%), model mendeteksinya sebagai **anomali** karena path length di Isolation Tree pendek (mudah diisolasi).

### Parameter Konfigurasi Pipeline (.env)

| Parameter | Default | Fungsi |
|-----------|---------|--------|
| `ANALYSIS_INTERVAL_MINUTES` | `5` | Interval analisis (menit) |
| `DATA_WINDOW_HOURS` | `1` | Data yang dibaca untuk prediksi (jam) |
| `RETRAIN_INTERVAL_MINUTES` | `1440` | Interval retrain model (menit, 0=disable) |
| `RETRAIN_DATA_HOURS` | `168` | Data untuk retrain (jam = 7 hari) |
| `RETRAIN_MIN_SAMPLES` | `100` | Minimum data mentah untuk retrain |
| `CONTAMINATION` | `0.05` | Persentase anomaly yang diharapkan (5%) |
| `N_ESTIMATORS` | `100` | Jumlah tree di Isolation Forest |

---

## 5. Sequence Diagram — Event System (Shutdown/Restart/Sleep)

Menunjukkan bagaimana Agent mendeteksi dan mengirim event sistem.

```mermaid
sequenceDiagram
    participant WIN as Windows OS
    participant SM as System Monitor
    participant EV as Event Sender
    participant API as FastAPI
    participant RQ as RQ Worker (system)
    participant DB as MySQL

    Note over WIN,SM: SHUTDOWN EVENT
    WIN->>SM: WM_QUERYENDSESSION
    SM->>SM: Cek _shutdown_sent flag
    alt Belum terkirim
        SM->>EV: send_shutdown_event()
        EV->>API: POST /api/v1/shutdown<br/>+ Header X-Real-IP
        API->>RQ: Enqueue (queue: system)
        RQ->>DB: INSERT INTO events (type=shutdown)
        SM->>SM: Set _shutdown_sent = True
    end
    SM-->>WIN: Return 1 (allow shutdown)

    Note over WIN,SM: SLEEP EVENT
    WIN->>SM: PBT_APMSUSPEND
    SM->>EV: send_sleep_event()
    EV->>API: POST /api/v1/sleep
    API->>RQ: Enqueue (queue: system)
    RQ->>DB: INSERT INTO events (type=sleep)

    Note over WIN,SM: RESTART DETECTION
    Note over WIN: PC dinyalakan kembali
    WIN->>SM: Agent startup
    SM->>SM: check_if_restart()
    SM->>WIN: Query Windows Event Log (ID 1074)
    
    alt Event berisi restart/reboot
        SM->>EV: send_restart_event()
        EV->>API: POST /api/v1/restart
        RQ->>DB: INSERT INTO events (type=restart)
    else Shutdown biasa
        SM->>EV: send_startup_event()
        EV->>API: POST /api/v1/startup
        RQ->>DB: INSERT INTO events (type=startup)
    end
```

### Shutdown Protection (3 Layer)

| Layer | Mekanisme | Kapan Terpanggil |
|-------|-----------|------------------|
| 1 | `WM_QUERYENDSESSION` (Win32 Message) | Saat Windows hendak shutdown — **paling awal** |
| 2 | `SetConsoleCtrlHandler` | Console close, logoff, shutdown event |
| 3 | `atexit` handler | Saat Python process exit |

> Semua layer berbagi flag `_shutdown_sent` agar event tidak dikirim ganda.

---

## 6. Activity Diagram — ML Pipeline

Flowchart lengkap proses machine learning dari pengumpulan data hingga notifikasi.

```mermaid
flowchart TD
    START(["Scheduler Trigger - Setiap 5 menit"]) --> READ

    READ["Baca data heartbeat<br/>dari MySQL (1 jam terakhir)"]
    READ --> CHECK_EMPTY{Data kosong?}

    CHECK_EMPTY -->|Ya| SKIP_END(["Pipeline selesai - Tidak ada data"])
    CHECK_EMPTY -->|Tidak| PREPROCESS

    PREPROCESS["Preprocessing<br/>Agregasi per device:<br/>cpu_mean, cpu_max,<br/>ram_mean, ram_max,<br/>disk_latest"]
    PREPROCESS --> CHECK_MODEL{File model ada?}

    CHECK_MODEL -->|Ya| LOAD["Load Model<br/>dari anomaly_model.pkl"]
    CHECK_MODEL -->|Tidak| TRAIN["Training Baru<br/>Isolation Forest<br/>n_estimators=100"]
    TRAIN --> SAVE["Simpan Model<br/>ke anomaly_model.pkl"]
    SAVE --> PREDICT

    LOAD --> CHECK_RETRAIN{Sudah waktunya retrain?}
    CHECK_RETRAIN -->|Tidak| PREDICT
    CHECK_RETRAIN -->|Ya| READ_TRAIN["Baca data training<br/>(7 hari terakhir)"]
    READ_TRAIN --> CHECK_MIN{Data >= 100 baris?}
    CHECK_MIN -->|Tidak| PREDICT
    CHECK_MIN -->|Ya| RETRAIN["Retrain Model<br/>dengan data terbaru"]
    RETRAIN --> SAVE_RETRAIN["Overwrite anomaly_model.pkl"]
    SAVE_RETRAIN --> PREDICT

    PREDICT["Prediksi<br/>predict: 1=normal, -1=anomaly<br/>decision_function: anomaly score"]
    PREDICT --> EVALUATE

    EVALUATE["Evaluasi Severity<br/>berdasarkan metrik aktual"]
    EVALUATE --> CHECK_ANOMALY{Ada anomaly?}

    CHECK_ANOMALY -->|Tidak| NORMAL(["Semua normal - No notification"])
    CHECK_ANOMALY -->|Ya| WRITE

    WRITE["Simpan Alert<br/>ke tabel alerts di MySQL"]
    WRITE --> SEVERITY{Severity?}

    SEVERITY -->|Critical atau Warning| TELEGRAM["Kirim Notifikasi<br/>ke Telegram"]
    SEVERITY -->|Normal| DONE

    TELEGRAM --> DONE(["Pipeline completed"])

    style START fill:#f39c12,stroke:#2c3e50,color:#fff
    style PREDICT fill:#e74c3c,stroke:#2c3e50,color:#fff
    style TRAIN fill:#9b59b6,stroke:#2c3e50,color:#fff
    style RETRAIN fill:#9b59b6,stroke:#2c3e50,color:#fff
    style TELEGRAM fill:#0088cc,stroke:#2c3e50,color:#fff
    style NORMAL fill:#27ae60,stroke:#2c3e50,color:#fff
    style DONE fill:#27ae60,stroke:#2c3e50,color:#fff
```

### Cara Kerja Isolation Forest

**Step 1 — Input Data (5 Fitur per Device):**

```mermaid
flowchart LR
    F1["cpu_mean<br/>8.9%"] --> F2["cpu_max<br/>25.0%"] --> F3["ram_mean<br/>65.5%"] --> F4["ram_max<br/>72.0%"] --> F5["disk_latest<br/>88.3%"]

    style F1 fill:#2c3e50,stroke:#1a1a2e,color:#fff
    style F2 fill:#2c3e50,stroke:#1a1a2e,color:#fff
    style F3 fill:#2c3e50,stroke:#1a1a2e,color:#fff
    style F4 fill:#2c3e50,stroke:#1a1a2e,color:#fff
    style F5 fill:#2c3e50,stroke:#1a1a2e,color:#fff
```

**Step 2 — Masuk ke 100 Isolation Trees:**

```mermaid
flowchart LR
    T1["Tree 1<br/>path: 7"] --> T2["Tree 2<br/>path: 9"] --> T3["Tree 3 ~ 99<br/>(98 trees)"] --> T4["Tree 100<br/>path: 8"]

    style T1 fill:#9b59b6,stroke:#6c3483,color:#fff
    style T2 fill:#9b59b6,stroke:#6c3483,color:#fff
    style T3 fill:#9b59b6,stroke:#6c3483,color:#fff
    style T4 fill:#9b59b6,stroke:#6c3483,color:#fff
```

**Step 3 — Hitung Skor dan Hasil:**

```mermaid
flowchart LR
    AVG["Rata-rata path: 8.2<br/>(panjang = sulit diisolasi)"] --> SCORE["raw_score: +0.35<br/>anomaly_score: 0.15"] --> RESULT["NORMAL"]

    style AVG fill:#3498db,stroke:#2874a6,color:#fff
    style SCORE fill:#f39c12,stroke:#d68910,color:#fff
    style RESULT fill:#27ae60,stroke:#1e8449,color:#fff
```

**Penjelasan Alur:**

1. **Input** — Data heartbeat diagregasi menjadi 5 fitur per device (cpu_mean, cpu_max, ram_mean, ram_max, disk_latest)
2. **Isolation Forest** — Setiap data dimasukkan ke 100 Random Trees. Setiap tree mencoba "mengisolasi" data dengan split acak. **Path length** = jumlah split yang dibutuhkan
3. **Rata-rata path** — Dihitung rata-rata path dari 100 trees. Data normal memiliki path **panjang** (sulit diisolasi), data anomali memiliki path **pendek** (mudah diisolasi)
4. **raw_score** — Output `decision_function()`. Positif = normal, negatif = anomali
5. **anomaly_score** — Dinormalisasi ke 0-1 dengan rumus `(0.5 - raw_score).clip(0, 1)`
6. **Hasil** — `predict()` menghasilkan 1 (normal) atau -1 (anomaly)

**Rumus Anomaly Score:**

```
anomaly_score = (0.5 - raw_score).clip(0, 1)
```

**Penjelasan `raw_score` (Skor Mentah):**

`raw_score` adalah output dari `model.decision_function()` milik scikit-learn Isolation Forest.
Nilainya menunjukkan seberapa "normal" atau "aneh" suatu data.

| raw_score | Arti |
|-----------|------|
| Positif besar (misal +0.35) | Data **sangat normal** — sulit diisolasi, butuh banyak split |
| Mendekati 0 | Data **di ambang batas** — bisa normal bisa anomali |
| Negatif (misal -0.15) | Data **anomali** — mudah diisolasi, hanya butuh sedikit split |

**Penjelasan `.clip(0, 1)` (Pembatas Nilai):**

`.clip(0, 1)` adalah fungsi NumPy yang membatasi nilai agar tetap dalam rentang 0 sampai 1.
Jika hasil < 0 maka dipaksa jadi 0. Jika hasil > 1 maka dipaksa jadi 1.

**Contoh Perhitungan Step-by-Step:**

| Device | raw_score | 0.5 - raw_score | .clip(0,1) | Hasil |
|--------|-----------|-----------------|------------|-------|
| A (normal) | +0.35 | 0.15 | **0.15** | Normal |
| B (ambang) | 0.00 | 0.50 | **0.50** | Warning |
| C (anomali) | -0.20 | 0.70 | **0.70** | Critical |
| D (sangat normal) | +0.80 | -0.30 | **0.00** | Normal (clip dari -0.30 ke 0) |

> Tanpa `.clip()`, Device D akan menghasilkan skor **-0.30** yang tidak valid.
> `.clip(0, 1)` memastikan anomaly_score selalu antara 0 (normal) dan 1 (sangat anomali).

| Kondisi | Severity | Aksi |
|---------|----------|------|
| prediction = 1 (normal) | Normal | Tidak ada alert |
| prediction = -1 dan CPU/RAM/Disk < 85% | Warning | Alert disimpan + Telegram |
| prediction = -1 dan CPU/RAM >= 85% atau Disk >= 90% | Critical | Alert disimpan + Telegram |
| prediction = -1 dan CPU/RAM >= 95% atau Disk >= 95% | Critical | Alert disimpan + Telegram |


### Pengaruh Data Training terhadap Deteksi Anomali

Isolation Forest adalah model **unsupervised** — model belajar pola `normal` **sepenuhnya dari data training**. Model tidak memiliki aturan tetap seperti `Disk > 90% = anomali`. Sebaliknya, model membandingkan data baru dengan **distribusi data training**.

```mermaid
flowchart TD
    A["Data Training 7 hari terakhir"] --> B{Distribusi data training}
    
    B -->|"Disk bervariasi 50-70% normal + 90-99% sedikit"| C["Model belajar: Disk 50-70% NORMAL, Disk lebih dari 90% ANOMALI"]
    
    B -->|"Disk selalu tinggi 94-95% semua data"| D["Model belajar: Disk 94-95% NORMAL, Tidak ada anomali"]
    
    C --> E["Disk 95% → Terdeteksi ANOMALI"]
    D --> F["Disk 95% → Dianggap NORMAL"]
    
    style A fill:#3498db,stroke:#2c3e50,color:#fff
    style C fill:#27ae60,stroke:#2c3e50,color:#fff
    style D fill:#e74c3c,stroke:#2c3e50,color:#fff
    style E fill:#27ae60,stroke:#2c3e50,color:#fff
    style F fill:#e74c3c,stroke:#2c3e50,color:#fff
```

**Contoh nyata:**

| Skenario | Data Training | Disk Saat Ini | Hasil Deteksi |
|----------|--------------|:-------------:|:-------------:|
| **A. Training bervariasi** | 95 data Disk 50-70% + 5 data Disk 90-99% | 95% | ANOMALI (menyimpang dari mayoritas) |
| **B. Training monoton** | 78 data semua Disk 95% | 95% | Normal (sama dengan semua training data) |

**Kunci keberhasilan deteksi:**

> Data training **harus memiliki variasi** — mayoritas data normal (CPU rendah, RAM sedang, Disk rendah) dengan sedikit data anomali. Semakin representatif data training terhadap pola normal sehari-hari, semakin akurat model mendeteksi penyimpangan.

---

## 7. Activity Diagram — Buffer Sync (Offline Resilience)

Flowchart mekanisme buffer saat API offline dan auto-sync saat kembali online.

```mermaid
flowchart TD
    START(["Agent mengumpulkan data"]) --> SEND

    SEND["Kirim ke FastAPI<br/>POST /api/v1/heartbeat"]
    SEND --> RESPONSE{Response?}

    RESPONSE -->|200 OK| SUCCESS(["Data terkirim<br/>Lanjut ke data berikutnya"])
    RESPONSE -->|Gagal - timeout/403/500| BUFFER

    BUFFER["Simpan ke SQLite Buffer<br/>agent/data/buffer.db"]
    BUFFER --> WAIT["Tunggu 30 detik"]
    WAIT --> RETRY

    RETRY["Ambil data dari buffer<br/>batch, FIFO"]
    RETRY --> RESEND["Kirim ulang ke FastAPI"]
    RESEND --> RETRY_RESPONSE{Response?}

    RETRY_RESPONSE -->|200 OK| DELETE["Hapus data dari buffer<br/>yang berhasil dikirim"]
    DELETE --> CHECK_BUFFER{Buffer masih ada data?}
    CHECK_BUFFER -->|Ya| RETRY
    CHECK_BUFFER -->|Tidak| SYNCED(["Buffer kosong<br/>Semua data tersinkronisasi"])

    RETRY_RESPONSE -->|Gagal| WAIT

    style BUFFER fill:#e74c3c,stroke:#2c3e50,color:#fff
    style SUCCESS fill:#27ae60,stroke:#2c3e50,color:#fff
    style SYNCED fill:#27ae60,stroke:#2c3e50,color:#fff
```

### Keuntungan Mekanisme Buffer

| Fitur | Manfaat |
|-------|---------|
| **Offline Resilience** | Data tidak hilang saat server down |
| **Auto Sync** | Otomatis kirim saat server kembali online |
| **FIFO Order** | Data dikirim sesuai urutan waktu |
| **Zero Data Loss** | Semua data monitoring tercatat |

---

## 8. Component Diagram

Menunjukkan arsitektur komponen dan dependensi antar service.

```mermaid
graph TB
    subgraph CLIENT["PC Client"]
        subgraph AGENT["Agent - Windows .exe"]
            COLLECTORS["Collectors<br/>heartbeat, network,<br/>process, application,<br/>event, system_monitor"]
            SENDER["Core Sender<br/>HTTP POST + X-Real-IP"]
            SCHEDULER_A["Scheduler<br/>APScheduler"]
            BUFFER["SQLite Buffer<br/>offline resilience"]

            COLLECTORS --> SENDER
            SCHEDULER_A --> COLLECTORS
            SENDER -->|Gagal| BUFFER
            BUFFER -->|Retry| SENDER
        end
    end

    subgraph SERVER["Docker Server"]
        subgraph API_LAYER["API Layer"]
            FASTAPI["FastAPI Collector<br/>Port 8011"]
            MIDDLEWARE["IP Filter Middleware"]
            FASTAPI --> MIDDLEWARE
        end

        subgraph QUEUE_LAYER["Queue Layer"]
            REDIS["Redis - Port 6379"]
            RQ_TEL["RQ Worker Telemetry"]
            RQ_EVT["RQ Worker Event"]
            RQ_SYS["RQ Worker System"]
        end

        subgraph DATA_LAYER["Data Layer"]
            MYSQL["MySQL 8.0 - Port 3306"]
        end

        subgraph ML_LAYER["ML Layer"]
            AINOC_SVC["AI NOC Service"]
            PREPROCESSOR["Preprocessor"]
            MODEL["Isolation Forest Model"]
            EVALUATOR["Evaluator"]
            NOTIFIER["Telegram Notifier"]
            MODEL_FILE["anomaly_model.pkl<br/>Docker Volume"]

            AINOC_SVC --> PREPROCESSOR
            PREPROCESSOR --> MODEL
            MODEL --> EVALUATOR
            EVALUATOR --> NOTIFIER
            MODEL --> MODEL_FILE
        end

        subgraph UI_LAYER["UI Layer"]
            DJANGO["Django Dashboard<br/>Port 8010"]
            PMA["phpMyAdmin<br/>Port 8012"]
        end
    end

    subgraph EXTERNAL["External"]
        TELEGRAM["Telegram API"]
    end

    SENDER -->|HTTP POST| FASTAPI
    MIDDLEWARE -->|Enqueue| REDIS
    REDIS --> RQ_TEL
    REDIS --> RQ_EVT
    REDIS --> RQ_SYS
    RQ_TEL -->|Save| MYSQL
    RQ_EVT -->|Save| MYSQL
    RQ_SYS -->|Save| MYSQL
    AINOC_SVC -->|Read| MYSQL
    AINOC_SVC -->|Write Alerts| MYSQL
    DJANGO -->|Read| MYSQL
    PMA -->|Manage| MYSQL
    NOTIFIER -->|Send Alert| TELEGRAM

    style CLIENT fill:#3498db,stroke:#2c3e50,color:#fff
    style FASTAPI fill:#e74c3c,stroke:#2c3e50,color:#fff
    style REDIS fill:#e91e63,stroke:#2c3e50,color:#fff
    style MYSQL fill:#f39c12,stroke:#2c3e50,color:#fff
    style MODEL fill:#9b59b6,stroke:#2c3e50,color:#fff
    style DJANGO fill:#27ae60,stroke:#2c3e50,color:#fff
    style TELEGRAM fill:#0088cc,stroke:#2c3e50,color:#fff
```

### Penjelasan Komponen

| Komponen | Layer | Fungsi |
|----------|-------|--------|
| **Collectors** | Agent | 6 modul pengumpul data: heartbeat, network, process, application, event, system_monitor |
| **Core Sender** | Agent | Mengirim data via HTTP POST dengan header `X-Real-IP`. IP dideteksi dari adapter yang routing ke server (`API_BASE_URL`), bukan `8.8.8.8` |
| **SQLite Buffer** | Agent | Menyimpan data secara lokal saat API offline, otomatis sync saat kembali online |
| **FastAPI + IP Middleware** | API | Menerima data dari agent, memvalidasi IP terhadap tabel `ip_whitelist` |
| **Redis** | Queue | Message broker yang menerima job dari FastAPI dan mendistribusikan ke worker |
| **RQ Workers (3x)** | Queue | Background processor yang memproses job dan menyimpan ke MySQL |
| **MySQL** | Data | Database utama yang menyimpan semua data monitoring, event, dan alert |
| **AI NOC** | ML | Service anomaly detection menggunakan Isolation Forest, membaca dari MySQL |
| **Telegram Notifier** | ML | Mengirim alert ke grup Telegram saat anomaly terdeteksi |
| **Django Dashboard** | UI | Web dashboard untuk administrator memantau status device dan alert |
| **phpMyAdmin** | UI | Tool web untuk manajemen database MySQL secara langsung |

### RQ Worker Queue Routing

| Queue | Worker | Data Types | Volume |
|-------|--------|------------|--------|
| `telemetry` | `noc_rq_telemetry` | heartbeat, network, process | Tinggi (~3 job/menit) |
| `event` | `noc_rq_event` | active_application, activity, alert | Sedang (~12 job/menit) |
| `system` | `noc_rq_system` | event (startup/shutdown/restart/lockscreen/sleep), device | Rendah (event-driven) |

---

## 9. Deployment Diagram

Menunjukkan infrastruktur fisik dan konfigurasi container Docker.

```mermaid
graph TB
    subgraph PC["PC Client - Windows"]
        EXE["NOC-Agent.exe<br/>PyInstaller build"]
        SQLITE["buffer.db - SQLite"]
        ENV_A[".env - API_URL, DEVICE_NAME"]

        EXE --> SQLITE
        EXE --> ENV_A
    end

    subgraph SERVER_HW["Server - Windows/Linux"]
        subgraph DOCKER["Docker Compose - name: ai-noc"]
            subgraph INFRA["Infrastructure"]
                C_MYSQL["noc_mysql<br/>mysql:8.0 - Port 3306<br/>restart: unless-stopped<br/>healthcheck: mysqladmin ping<br/>start_period: 30s"]
                C_REDIS["noc_redis<br/>redis:7-alpine - Port 6379<br/>restart: unless-stopped<br/>healthcheck: redis-cli ping<br/>start_period: 10s"]
            end

            subgraph BACKEND["Backend Services"]
                C_FASTAPI["noc_fastapi<br/>python:3.11-slim - Port 8011<br/>depends_on: mysql healthy, redis healthy<br/>+ Retry logic startup"]
                C_RQ1["noc_rq_telemetry<br/>Queue: telemetry<br/>depends_on: mysql healthy, redis healthy"]
                C_RQ2["noc_rq_event<br/>Queue: event<br/>depends_on: mysql healthy, redis healthy"]
                C_RQ3["noc_rq_system<br/>Queue: system<br/>depends_on: mysql healthy, redis healthy"]
            end

            subgraph APPSERVICES["Application Services"]
                C_AI["noc_ai<br/>AI NOC - Isolation Forest<br/>depends_on: mysql healthy<br/>volume: ai_models"]
                C_DJANGO["noc_django<br/>Gunicorn + Django - Port 8010"]
                C_PMA["noc_phpmyadmin<br/>phpMyAdmin - Port 8012"]
            end

            subgraph VOLUMES["Persistent Volumes"]
                V_MYSQL["mysql_data"]
                V_REDIS["redis_data"]
                V_MODEL["ai_models - anomaly_model.pkl"]
            end
        end

        ENV_S[".env - Database, Telegram, Ports, ML config"]
    end

    subgraph NETWORK["Network"]
        LAN["LAN / WiFi - 192.168.x.x"]
    end

    subgraph CLOUD["Cloud"]
        TG_API["Telegram Bot API"]
    end

    PC -->|HTTP :8011| LAN
    LAN --> C_FASTAPI
    C_AI -->|HTTPS| TG_API
    C_MYSQL --> V_MYSQL
    C_REDIS --> V_REDIS
    C_AI --> V_MODEL

    style PC fill:#3498db,stroke:#2c3e50,color:#fff
    style DOCKER fill:#1a1a2e,stroke:#16213e,color:#fff
    style C_MYSQL fill:#f39c12,stroke:#2c3e50,color:#fff
    style C_REDIS fill:#e91e63,stroke:#2c3e50,color:#fff
    style C_FASTAPI fill:#e74c3c,stroke:#2c3e50,color:#fff
    style C_AI fill:#9b59b6,stroke:#2c3e50,color:#fff
    style C_DJANGO fill:#27ae60,stroke:#2c3e50,color:#fff
```

### Auto-Setup Saat Pertama Kali (Fresh Server)

Saat `docker-compose up -d` dijalankan di **komputer baru**, semua tabel dan konfigurasi dibuat **otomatis** tanpa setup manual:

| Urutan | Container | Proses Otomatis |
|:------:|-----------|----------------|
| 1 | **MySQL** | Buat database `noc_database` (dari `MYSQL_DATABASE` di `.env`) |
| 2 | **FastAPI** | `Base.metadata.create_all()` → buat 8 tabel NOC + 7 FK constraint |
| 3 | **RQ Workers** | Import semua model termasuk `Device` untuk FK resolution |
| 4 | **Django** | `entrypoint.sh` → `migrate` (buat tabel auth/admin/session) + buat superuser `admin/admin` |
| 5 | **AI NOC** | Training model otomatis saat data cukup (`RETRAIN_MIN_SAMPLES`) |

### Startup Order (Setelah Restart Server)

```mermaid
flowchart LR
    M["1. MySQL<br/>start_period: 30s"] -->|healthy| F["3. FastAPI<br/>retry 10x"]
    R["2. Redis<br/>start_period: 10s"] -->|healthy| F
    M -->|healthy| RQ["4. RQ Workers x3"]
    R -->|healthy| RQ
    M -->|healthy| AI["5. AI NOC"]
    M -->|healthy| DJ["6. Django"]
    M -->|healthy| PMA["7. phpMyAdmin"]

    style M fill:#f39c12,stroke:#2c3e50,color:#fff
    style R fill:#e91e63,stroke:#2c3e50,color:#fff
    style F fill:#e74c3c,stroke:#2c3e50,color:#fff
```

**Penjelasan Startup Order:**

Setelah server restart (atau `docker compose up`), container dimulai dalam urutan berikut:

| Urutan | Container | Syarat | Waktu Tunggu | Keterangan |
|--------|-----------|--------|-------------|------------|
| 1 | **MySQL** | - | `start_period: 30s` | Database harus siap paling awal. Healthcheck: `mysqladmin ping` |
| 2 | **Redis** | - | `start_period: 10s` | Message broker harus siap sebelum worker. Healthcheck: `redis-cli ping` |
| 3 | **FastAPI** | MySQL ✅ + Redis ✅ | Retry 10x (interval 5s) | Baru start setelah MySQL & Redis healthy. Ada retry logic internal jika DB belum siap |
| 4 | **RQ Workers (x3)** | MySQL ✅ + Redis ✅ | - | 3 worker (telemetry, event, system) start paralel setelah infra healthy |
| 5 | **AI NOC** | MySQL ✅ | - | Hanya butuh MySQL untuk baca heartbeat & tulis alert |
| 6 | **Django** | MySQL ✅ | - | Dashboard baca data dari MySQL |
| 7 | **phpMyAdmin** | MySQL ✅ | - | Tool manajemen database |

> Semua container menggunakan `restart: unless-stopped` — otomatis restart jika crash, tapi tidak restart jika dihentikan manual (`docker stop`).

> Jika MySQL belum healthy, container yang bergantung padanya **tidak akan dimulai** sampai healthcheck berhasil. Ini mencegah error koneksi saat boot.

---

## 10. State Diagram — Device Lifecycle

Menunjukkan transisi status device dari registrasi hingga deteksi anomali.

**Registrasi dan Koneksi:**

```mermaid
flowchart LR
    START(["● Start"]) -->|Agent start| REG["Registering"]
    REG -->|Registrasi OK| ONLINE[" Online"]
    REG -->|API down| OFFLINE["Offline"]
    OFFLINE -->|Reconnect| ONLINE

    style START fill:#2c3e50,stroke:#2c3e50,color:#fff
    style ONLINE fill:#27ae60,stroke:#1e8449,color:#fff
    style OFFLINE fill:#c0392b,stroke:#922b21,color:#fff
    style REG fill:#8e44ad,stroke:#6c3483,color:#fff
```

**Sleep dan Lockscreen:**

```mermaid
flowchart LR
    ONLINE["Online"] -->|Sleep| SLEEP["Sleep"]
    SLEEP -->|Wake up| ONLINE2["Online"]
    ONLINE["Online"] -->|Win+L| LOCKED["Locked"]
    LOCKED -->|Unlock| ONLINE2["Online"]

    style ONLINE fill:#27ae60,stroke:#1e8449,color:#fff
    style ONLINE2 fill:#27ae60,stroke:#1e8449,color:#fff
    style SLEEP fill:#3498db,stroke:#2874a6,color:#fff
    style LOCKED fill:#f39c12,stroke:#d68910,color:#fff
```

**Shutdown, Anomaly, dan Offline:**

```mermaid
flowchart LR
    ONLINE["Online"] -->|Shutdown| SD["Shutting Down"]
    SD -->|PC mati| END(["● End"])
    SD -->|Restart| ONLINE2["Online"]

    ONLINE -->|Anomali| AN["Anomaly"]
    AN -->|Resolved| ONLINE2

    ONLINE -->|Timeout| OF["Offline"]
    OF -->|Reconnect| ONLINE2

    style ONLINE fill:#27ae60,stroke:#1e8449,color:#fff
    style ONLINE2 fill:#27ae60,stroke:#1e8449,color:#fff
    style SD fill:#7f8c8d,stroke:#566573,color:#fff
    style AN fill:#e74c3c,stroke:#c0392b,color:#fff
    style OF fill:#c0392b,stroke:#922b21,color:#fff
    style END fill:#2c3e50,stroke:#2c3e50,color:#fff
```

**Transisi antar state:**

| Dari | Ke | Trigger |
|------|-----|---------|
| `[*]` | Registering | Agent pertama kali dijalankan |
| Registering | Online | POST /device-registration → 200 OK |
| Registering | Offline | Registrasi gagal (API down) |
| Online | Online | Heartbeat diterima (setiap 60 detik) |
| Online | Sleep | Event `PBT_APMSUSPEND` terdeteksi |
| Online | Locked | Event lockscreen (Win+L) terdeteksi |
| Online | Shutting Down | Event shutdown/restart terdeteksi |
| Online | Anomaly Detected | AI NOC mendeteksi anomali (score tinggi) |
| Online | Offline | Heartbeat timeout (lebih dari 3 menit) |
| Sleep | Online | Wake up (`PBT_APMRESUMESUSPEND`) |
| Locked | Online | Unlock screen |
| Shutting Down | `[*]` | PC mati |
| Shutting Down | Online | Restart → startup event dikirim |
| Anomaly Detected | Online | Alert resolved / metrik kembali normal |
| Offline | Online | Heartbeat diterima kembali (buffer sync) |

### Penjelasan State

| State | Deskripsi | Trigger Masuk | Trigger Keluar |
|-------|-----------|---------------|----------------|
| **Registering** | Device baru, belum terdaftar | Agent pertama kali jalan | Registrasi berhasil/gagal |
| **Online** | Normal, heartbeat aktif | Heartbeat diterima | Timeout/event/anomaly |
| **Sleep** | PC dalam mode sleep | PBT_APMSUSPEND | Wake up |
| **Locked** | Layar terkunci | Win+L / timeout screen | Unlock |
| **ShuttingDown** | PC sedang dimatikan | WM_QUERYENDSESSION | PC mati / restart |
| **Anomaly** | AI NOC mendeteksi masalah | Anomaly score tinggi | Metrik kembali normal |
| **Offline** | Agent tidak bisa kontak API | Timeout / API down | API kembali online |

---

## Ringkasan Arsitektur

```mermaid
graph LR
    A["Agent<br/>Windows .exe"] -->|"HTTP POST<br/>+ X-Real-IP"| B["FastAPI<br/>Port 8011"]
    B -->|Enqueue| C["Redis"]
    C -->|Process| D["RQ Workers x3"]
    D -->|Save| E["MySQL"]
    E -->|Read| F["AI NOC"]
    F -->|Alert| G["Telegram"]
    E -->|Read| H["Django Dashboard<br/>Port 8010"]

    style A fill:#3498db,stroke:#2c3e50,color:#fff
    style B fill:#e74c3c,stroke:#2c3e50,color:#fff
    style C fill:#e91e63,stroke:#2c3e50,color:#fff
    style E fill:#f39c12,stroke:#2c3e50,color:#fff
    style F fill:#9b59b6,stroke:#2c3e50,color:#fff
    style G fill:#0088cc,stroke:#2c3e50,color:#fff
    style H fill:#27ae60,stroke:#2c3e50,color:#fff
```

| Komponen | Teknologi | Fungsi |
|----------|-----------|--------|
| **Agent** | Python + PyInstaller (.exe) | Monitoring PC client |
| **FastAPI** | Python + Uvicorn | API endpoint penerima data |
| **Redis** | Redis 7 Alpine | Message broker (antrian task) |
| **RQ Workers** | Python-RQ (3 dedicated) | Background job processor |
| **MySQL** | MySQL 8.0 | Database utama |
| **AI NOC** | scikit-learn (Isolation Forest) | Anomaly detection + Telegram |
| **Django** | Django + Gunicorn | Dashboard monitoring |
| **phpMyAdmin** | phpMyAdmin | Database management |