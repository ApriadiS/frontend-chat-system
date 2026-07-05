# 💬 ChatMVP - Frontend Web Application

![Svelte](https://img.shields.io/badge/Svelte-4A4A55?style=for-the-badge&logo=svelte&logoColor=FF3E00)
![Vite](https://img.shields.io/badge/Vite-B73BFE?style=for-the-badge&logo=vite&logoColor=FFD62E)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)
![Supabase](https://img.shields.io/badge/Supabase-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white)
![Vercel](https://img.shields.io/badge/Vercel-000000?style=for-the-badge&logo=vercel&logoColor=white)

Selamat datang di repositori Frontend **ChatMVP**! Ini adalah antarmuka pengguna (UI) modern, responsif, dan *real-time* yang dibangun untuk memenuhi Ujian Akhir Semester mata kuliah Komputasi Paralel Dan Terdistribusi.

Aplikasi ini berinteraksi langsung dengan backend Java (Javalin) via WebSocket dan menggunakan Supabase sebagai layanan Autentikasi terintegrasi.

---

## ✨ Fitur Utama

- ⚡ **Real-Time WebSocket:** Pertukaran pesan instan tanpa jeda menggunakan API WebSocket native.
- 🔐 **Secure Authentication:** Sistem Login, Register, dan Reset Password yang ditenagai oleh Supabase Auth.
- 📱 **Mobile Responsive:** Tata letak antarmuka yang dioptimalkan secara penuh untuk perangkat seluler (Mobile-first) maupun layar Desktop menggunakan Tailwind CSS.
- 💬 **Multi-Channel Chat:** Dukungan untuk Siaran Global (Broadcast), Obrolan Pribadi (Private), dan Obrolan Grup.
- 🛡️ **Graceful Error Handling:** Umpan balik visual (Toast/Alert) secara langsung ketika pesan mengandung kata terlarang atau koneksi terputus.

---

## 🛠️ Teknologi yang Digunakan

| Kategori | Teknologi | Deskripsi |
| :--- | :--- | :--- |
| **Framework** | **Svelte** | Framework reaktif berbasis komponen tanpa virtual DOM. |
| **Build Tool** | **Vite** | Frontend tooling yang sangat cepat untuk *hot-reload* & *bundling*. |
| **Styling** | **Tailwind CSS** | Framework CSS utility-first untuk desain responsif. |
| **BaaS / Auth**| **Supabase** | Backend-as-a-Service untuk manajemen pengguna dan autentikasi. |
| **Deployment**| **Vercel** | Hosting cloud yang dioptimalkan untuk Single Page Applications (SPA). |

---

## ⚙️ Konfigurasi Environment

Sebelum menjalankan proyek ini di lokal, Anda wajib membuat file `.env` di *root* direktori proyek. 

> **Catatan:** Jangan pernah mengunggah kredensial asli ke repositori publik. Gunakan `.env` lokal untuk pengembangan.

Buat file `.env` dan isi dengan variabel berikut:

```env
# Konfigurasi Supabase (Dapatkan di Dashboard Supabase > Project Settings > API)
VITE_SUPABASE_URL=https://[PROJECT_ID].supabase.co
VITE_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsIn...[YOUR_ANON_KEY]

# Konfigurasi WebSocket Backend (Gunakan IP VPS/Domain atau Localhost)
VITE_WS_URL=ws://[IP_VPS_KAMU]:7070/chat

```

---

## 🚀 Cara Instalasi & Menjalankan di Lokal

> ⚠️ **PERHATIAN (Ketergantungan Sistem):**
> Aplikasi frontend ini **tidak dapat berjalan secara mandiri**. Anda diwajibkan untuk menjalankan server backend terlebih dahulu agar fitur autentikasi dan WebSocket dapat terhubung.
> 👉 **[Kloning & Jalankan Repositori Backend Di Sini](https://github.com/ApriadiS/Apriadi_Salim_2410010605_4A_PBO1-backend-chat-system.git)**

Jika backend sudah berjalan, pastikan komputer Anda sudah terinstal **Node.js** (versi 18+ direkomendasikan), lalu ikuti langkah berikut:

1. **Kloning Repositori:**
```bash
git clone [https://github.com/ApriadiS/frontend-chat-system.git](https://github.com/ApriadiS/frontend-chat-system.git)
cd frontend-chat-system

```


2. **Instalasi Dependensi:**
```bash
npm install

```


3. **Jalankan Development Server:**
```bash
npm run dev

```


*Aplikasi akan berjalan secara lokal, biasanya di `http://localhost:5173`.*

---

## 🌐 Panduan Deployment (Vercel)

Proyek ini telah dikonfigurasi agar siap di-*deploy* ke **Vercel**.

1. **Routing SPA:**
File `vercel.json` telah disertakan di *root* repositori untuk mencegah masalah `404 Not Found` saat pengguna memuat ulang (*refresh*) halaman:
```json
{
  "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }]
}

```


2. **Environment Variables Vercel:**
Saat menghubungkan repositori Github ke Vercel, pastikan Anda menambahkan ketiga variabel dari file `.env` di atas ke dalam menu **Settings > Environment Variables** di Dashboard Vercel sebelum menekan tombol *Deploy*.

---

## 👥 Tim Pengembang (UAS Komputasi Paralel Dan Terdistribusi July 2026)

Proyek kolaboratif ini dibangun bersama oleh tim:

* 🛠️ **Apriadi Salim** (2410010605) - *Back End & Infra*
* 👨‍💻 **Muhammad Reynaldy Abhista Putra** (2410010010) - *Front End*
* 👨‍💻 **Moh Hafiz Anshari** (2410010142) - *Front End*
* 👨‍💻 **M Reza Maulani Aditya** (2410010638) - *Front End*

> Dibuat dengan 💻 dan ☕ untuk memenuhi penilaian mata kuliah Komputasi Paralel Dan Terdistribusi.
