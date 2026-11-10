# NutriVillage AI — Aplikasi Flutter

Aplikasi mobile keluarga + petugas SPPG. Memanggil **FastAPI langsung**
(Bearer JWT), bukan proxy Next.js, karena lapisan web menyimpan sesi di cookie
dan mengubah bentuk data ke camelCase.

## Menjalankan

Prasyarat di mesin ini: Flutter 3.47.9 di `C:\dev\flutter`, Android SDK di
`C:\dev\android-sdk`, JDK 17 di `C:\dev\jdk17`.

```powershell
# 1. Jalankan backend (butuh MySQL) dari folder nutrivillage-ai-backend
.\.venv\Scripts\python.exe scripts\init_db.py
.\.venv\Scripts\python.exe -m uvicorn app.main:app --reload --port 8000

# 2. Jalankan aplikasi. API_BASE_URL di atas mengarah ke host dari emulator Android.
cd ..\nutrivillage_app
flutter run --dart-define=API_BASE_URL=http://10.0.2.2:8000

# Perangkat fisik? Pakai IP LAN mesin yang menjalankan backend:
flutter run --dart-define=API_BASE_URL=http://192.168.1.10:8000
```

`http://` hanya diizinkan pada build debug (`android/app/src/debug/AndroidManifest.xml`).
Build rilis hanya mau HTTPS, jadi backend produksi harus di belakang TLS.

## Periksa

```powershell
flutter analyze      # harus "No issues found"
flutter test         # uji kontrak autentikasi
flutter build apk --debug
```

## Isi saat ini

Sudah jalan:

- Penyimpanan token aman (access di memori + secure storage, refresh token
  dirotasi otomatis saat 401).
- Login & daftar (peran keluarga / petugas SPPG), validasi mengikuti skema
  Pydantic backend agar 422 tidak perlu pulang-pergi.
- Pemulihan sesi saat aplikasi dibuka, logout, dan penolakan peran admin ke
  layar "panel admin hanya di web".
- Kerangka navigasi: bottom nav dua varian (keluarga & SPPG) + tema token web.

Masih placeholder: Beranda, Jelajah, Rencana, Belanja, Dashboard SPPG, Sekolah.

## Struktur

```
lib/
  main.dart
  app/         theme, router, shell navigasi
  core/        config (--dart-define), token_store
  features/auth/  models, api_client, auth_api, session_controller, login_page
```
