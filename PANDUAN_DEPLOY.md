# 🚀 PANDUAN LENGKAP PUBLISH & DEPLOY APLIKASI PRESENSI QR CODE
## SMP Nurul Huda Jayapura (Next.js 15 App Router + Firebase + Google Sheets + PWA)

Dokumen ini berisi panduan langkah-demi-langkah yang praktis, mudah dipahami, dan mendalam untuk mempublikasikan (men-deploy) aplikasi Sistem Presensi Digital SMP Nurul Huda Jayapura agar dapat diakses 24 jam secara online oleh seluruh Guru, Siswa, Petugas Piket, dan Wali Murid.

---

## 📌 DAFTAR ISI
1. [Metode 1: Publikasi Instan 1-Klik via Google AI Studio (Paling Mudah & Gratis)](#metode-1-publikasi-instan-1-klik-via-google-ai-studio)
2. [Metode 2: Deploy ke Vercel (Sangat Direkomendasikan untuk Next.js & Custom Domain)](#metode-2-deploy-ke-vercel)
3. [Metode 3: Deploy ke VPS / Server Sekolah (Ubuntu/Debian dengan PM2 & Nginx)](#metode-3-deploy-ke-vps--server-sekolah)
4. [Metode 4: Deploy Menggunakan Docker Container](#metode-4-deploy-menggunakan-docker-container)
5. [Konfigurasi Database & Layanan Pendukung (Wajib)](#konfigurasi-database--layanan-pendukung-wajib)
6. [Pemasangan Aplikasi di HP (PWA Tanpa Play Store)](#pemasangan-aplikasi-di-hp-pwa-tanpa-play-store)
7. [Checklist Pengujian Pasca-Deploy & Troubleshooting](#checklist-pengujian-pasca-deploy--troubleshooting)

---

## METODE 1: Publikasi Instan 1-Klik via Google AI Studio
*Cocok untuk: Sekolah yang ingin langsung online tanpa ribet setup server atau coding sama sekali.*

Aplikasi ini sudah berjalan di ekosistem Google Cloud Run milik AI Studio. Anda bisa mempublikasikannya langsung:

### Langkah-langkah:
1. **Buka Google AI Studio** pada proyek aplikasi ini.
2. Di pojok kanan atas jendela antarmuka AI Studio, cari tombol **"Share"** atau **"Publish / Deploy"**.
3. Pilih visibilitas:
   - **Public**: Dapat dibuka oleh siapa saja yang memiliki link (Direkomendasikan agar siswa & wali murid bisa membuka portal).
4. Klik **Deploy / Publish**.
5. Salin URL publik resmi Anda yang berakhiran `.run.app`, contoh:
   ```
   https://ais-pre-pe4adta6rgzxnkwdsgfufq-1030323294793.asia-southeast1.run.app
   ```
6. **Selesai!** Link ini sudah aktif 24 jam dengan protokol keamanan HTTPS otomatis dari Google.

---

## METODE 2: Deploy ke Vercel
*Cocok untuk: Menggunakan custom domain resmi sekolah (contoh: `presensi.smpnurulhudajayapura.sch.id`), performa CDN super cepat, dan auto-deploy saat update kode.*

Vercel adalah platform cloud resmi pembuat Next.js dan menyediakan paket gratis (*Hobby Plan*) yang sangat memadai untuk sekolah.

### Langkah 1: Simpan Kode ke GitHub
1. Di AI Studio, klik menu titik tiga (atau ikon GitHub) -> **Export to GitHub** (atau unduh zip proyek).
2. Buat repositori baru di akun GitHub Anda (misal: `presensi-qr-smp-nurul-huda`).
3. Unggah seluruh berkas proyek ke repositori tersebut.

### Langkah 2: Hubungkan ke Vercel
1. Buka [https://vercel.com](https://vercel.com) dan buat akun/login menggunakan akun **GitHub**.
2. Di dashboard Vercel, klik tombol **"Add New..."** lalu pilih **"Project"**.
3. Pada daftar repositori GitHub Anda, klik **"Import"** pada repositori `presensi-qr-smp-nurul-huda`.

### Langkah 3: Pengaturan Build Vercel
1. **Framework Preset**: Vercel otomatis mendeteksi **Next.js**.
2. **Root Directory**: Biarkan `./` (default).
3. **Build Command**: `next build` (otomatis).
4. **Output Directory**: `.next` (otomatis).
5. **Install Command**: `npm install` (otomatis).

### Langkah 4: Environment Variables (Variabel Lingkungan)
Tambahkan variabel lingkungan di menu **Environment Variables** Vercel (bila diperlukan):
- `GEMINI_API_KEY`: *(Opsional, masukkan API key Google Gemini jika fitur asisten AI aktif)*
- `NEXT_PUBLIC_APP_URL`: Masukkan URL domain Anda (contoh: `https://presensi.smpnurulhuda.sch.id`)

*Catatan: Konfigurasi Firebase (`firebase-applet-config.json`) sudah ada di dalam berkas proyek, sehingga Firestore langsung tersambung otomatis.*

### Langkah 5: Klik Deploy
1. Klik tombol **"Deploy"**.
2. Tunggu proses build selama 1–2 menit hingga muncul kembang api pertanda sukses.
3. Anda akan mendapatkan URL publik gratis, seperti:
   `https://presensi-qr-smp-nurul-huda.vercel.app`

### Langkah 6: Memasang Domain Sekolah (.sch.id) di Vercel
1. Di dashboard proyek Vercel, buka tab **Settings** -> **Domains**.
2. Masukkan nama domain yang diinginkan, misal: `presensi.smpnurulhudajayapura.sch.id`.
3. Klik **Add**.
4. Vercel akan memberikan catatan DNS:
   - **Type**: `CNAME`
   - **Name**: `presensi`
   - **Target / Value**: `cname.vercel-dns.com`
5. Buka panel pengelola domain sekolah Anda (cPanel/Cloudflare/Niagahoster/Rumahweb), tambahkan record CNAME tersebut.
6. Dalam hitungan menit, sertifikat SSL HTTPS otomatis diterbitkan dan aktif.

---

## METODE 3: Deploy ke VPS / Server Sekolah
*Cocok untuk: Sekolah yang memiliki server lokal sendiri, Komputer Lab, atau VPS Linux (Ubuntu 22.04 / 24.04).*

### Persyaratan Server:
- OS: Ubuntu 20.04/22.04/24.04 LTS atau Debian 11/12.
- RAM: Minimal 1 GB (Direkomendasikan 2 GB + Swap 2 GB).
- Node.js: Versi 20 LTS atau versi 22 LTS.

### Langkah-langkah Terminal:

#### 1. Update Server & Pasang Node.js 20 LTS:
```bash
sudo apt update && sudo apt upgrade -y
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs git nginx
node -v # pastikan v20.x.x
npm -v
```

#### 2. Pasang PM2 (Process Manager agar aplikasi jalan terus di latar belakang):
```bash
sudo npm install -g pm2
```

#### 3. Unduh Kode Proyek:
```bash
cd /var/www
sudo git clone https://github.com/username/presensi-qr-smp-nurul-huda.git
cd presensi-qr-smp-nurul-huda
sudo chown -R $USER:$USER /var/www/presensi-qr-smp-nurul-huda
```

#### 4. Install Dependensi & Build Aplikasi:
```bash
npm install
npm run build
```

#### 5. Jalankan Aplikasi dengan PM2:
```bash
pm2 start npm --name "presensi-qr" -- start
pm2 save
pm2 startup
```
*Aplikasi kini aktif berjalan di `http://localhost:3000`.*

#### 6. Konfigurasi Nginx Reverse Proxy:
Buat berkas konfigurasi Nginx:
```bash
sudo nano /etc/nginx/sites-available/presensi
```
Tempelkan konfigurasi berikut:
```nginx
server {
    listen 80;
    server_name presensi.smpnurulhudajayapura.sch.id; # Ganti dengan domain atau IP Server Anda

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
Aktifkan dan restart Nginx:
```bash
sudo ln -s /etc/nginx/sites-available/presensi /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

#### 7. Pasang SSL Gratis (HTTPS) dengan Certbot Let's Encrypt:
```bash
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d presensi.smpnurulhudajayapura.sch.id
```
Pilih opsi redirect ke HTTPS. Kamera scan QR wajib menggunakan protokol HTTPS agar browser mengizinkan akses kamera HP/laptop.

---

## METODE 4: Deploy Menggunakan Docker Container
*Cocok untuk: Cloud modern seperti GCP Cloud Run, AWS ECS, DigitalOcean App Platform, atau Kubernetes.*

Buat berkas `Dockerfile` di root folder proyek:
```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/package*.json ./
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/firebase-applet-config.json ./firebase-applet-config.json

EXPOSE 3000
CMD ["npm", "run", "start"]
```

Jalankan container:
```bash
docker build -t presensi-smp-nurul-huda .
docker run -d -p 3000:3000 --name presensi-app presensi-smp-nurul-huda
```

---

## KONFIGURASI DATABASE & LAYANAN PENDUKUNG (WAJIB)

Agar seluruh fitur berjalan 100% sempurna setelah aplikasi online, selesaikan 3 konfigurasi berikut:

### 1. Database Cloud Firestore
- Proyek ini sudah otomatis terhubung dengan Firebase Firestore (`ai-studio-presensiqrcodesm-7dc2ece3-d694-4234-9752-683bedf830d5`).
- Berkas aturan keamanan `firestore.rules` sudah aktif melindungi data siswa dan hak akses administrator.
- Jika Anda memindahkan proyek ke akun Firebase Anda sendiri, cukup ganti berkas `firebase-applet-config.json` dengan konfigurasi Web App dari Firebase Console Anda.

### 2. Live Sync Google Sheets (Database Cadangan & Arsip Guru)
Aplikasi memiliki fitur sinkronisasi real-time ke Google Spreadsheet sekolah:
1. Buka Google Spreadsheet baru di akun Google Drive resmi sekolah.
2. Klik menu **Ekstensi** -> **Apps Script**.
3. Di dalam aplikasi presensi, buka tab menu **"Live Sheets"** -> klik **"Lihat Kode Apps Script"**.
4. Salin kode `Code.gs` dan tempelkan ke editor Google Apps Script.
5. Klik **Deploy** -> **New Deployment** -> Pilih type **Web App**:
   - **Execute as**: *Me (email akun sekolah Anda)*
   - **Who has access**: *Anyone (Siapa saja)*
6. Salin **Web App URL** (akhiran `/exec`) dan tempelkan pada kolom integrasi di menu **Live Sheets** aplikasi.
7. Setiap siswa melakukan scan presensi di gerbang, data otomatis muncul di baris Google Spreadsheet dalam 1 detik!

### 3. WhatsApp Gateway Notifikasi Otomatis
Untuk mengirim laporan kehadiran harian siswa langsung ke nomor WhatsApp wali murid:
1. Buka menu **WhatsApp Gateway** di aplikasi presensi.
2. Pilih provider:
   - **Meta Cloud API Resmi**: Gratis 1.000 percakapan/bulan dari Meta/Facebook Developers.
   - **Fonnte / Wablas / Qontak**: Menggunakan nomor WhatsApp sekolah yang ada tanpa verifikasi bisnis Facebook.
3. Masukkan **Access Token** & **Nomor Pengirim**.
4. Aktifkan sakelar:
   - Kirim otomatis saat scan Masuk Pagi.
   - Kirim otomatis saat scan Pulang Sore.
   - Ringkasan rekapitulasi harian pukul 16:00 WIT.

---

## PEMASANGAN APLIKASI DI HP (PWA TANPA PLAY STORE)
Aplikasi ini sudah berstandar **Progressive Web App (PWA)** dengan service worker dan manifest resmi:

### Untuk HP Android (Chrome):
1. Buka tautan website presensi sekolah di Google Chrome.
2. Tekan tombol titik tiga di pojok kanan atas browser.
3. Pilih **"Instal aplikasi"** atau **"Tambahkan ke Layar Utama" (Add to Home screen)**.
4. Ikon resmi **SMP Nurul Huda Jayapura** akan muncul di menu HP Anda dan dapat dibuka seperti aplikasi native dari Play Store.

### Untuk iPhone / iPad (Safari):
1. Buka tautan website presensi sekolah di browser Safari.
2. Tekan tombol **Share** (kotak dengan panah ke atas di bagian bawah layar).
3. Gulir ke bawah dan pilih **"Tambah ke Layar Utama" (Add to Home Screen)**.
4. Tekan **Tambah** di pojok kanan atas.

---

## CHECKLIST PENGUJIAN PASCA-DEPLOY & TROUBLESHOOTING

Sebelum mengumumkan link resmi ke seluruh wali murid, lakukan checklist pengujian berikut:

| Item Pengujian | Cara Uji | Status Target |
|---|---|---|
| **Koneksi HTTPS** | Buka URL di browser, pastikan ikon gembok aman menyala hijau | Wajib HTTPS agar kamera dapat terbuka |
| **Izin Kamera** | Buka menu Pemindai QR, klik Izinkan Akses Kamera | Video kamera aktif mulus di HP & Laptop |
| **Login Administrator** | Masuk dengan user `admin` dan pass `admin123` | Berhasil masuk ke dashboard kontrol |
| **Login Guru Piket** | Masuk dengan user `guru` dan pass `guru123` | Berhasil membuka scanner & generator sesi |
| **Login Portal Siswa** | Masuk dengan NISN siswa (misal: `0061234501`) pass `1234` | Kartu digital & status presensi tampil |
| **Test Scan Presensi** | Lakukan test scan salah satu barcode kartu siswa | Chime berbunyi, suara sambutan WIT terdengar, data bertambah di tabel |
| **Sinkronisasi Sheets** | Periksa Google Spreadsheet sekolah | Baris baru masuk otomatis |
| **Penyimpanan Offline** | Matikan Wi-Fi/data HP lalu coba scan kartu | Tetap tercatat lokal dan terkirim otomatis saat online kembali |

---

## 📞 BANTUAN & DUKUNGAN TEKNIS
Jika Anda menemukan kendala saat proses deploy:
1. Pastikan port 3000 tidak terblokir firewall jika menggunakan VPS.
2. Pastikan domain selalu menggunakan **HTTPS** (karena browser melarang akses kamera di HTTP biasa).
3. Hubungi Tim IT & Kesiswaan SMP Nurul Huda Jayapura untuk pendampingan implementasi lebih lanjut.
