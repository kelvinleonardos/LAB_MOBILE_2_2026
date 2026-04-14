# TP2 — Aplikasi Instagram Clone (Android)

> Aplikasi Android yang meniru tampilan dan fitur utama Instagram, dibangun sebagai tugas praktikum (TP2). Mencakup halaman Home Feed, Profile, Story, Highlights, dan fitur tambah postingan.

---

## Daftar Isi

- [Tentang Proyek](#tentang-proyek)
- [Fitur Utama](#fitur-utama)
- [Screenshot](#screenshot)
- [Arsitektur & Struktur Proyek](#arsitektur--struktur-proyek)
- [Alur Navigasi](#alur-navigasi)
- [Komponen Teknis](#komponen-teknis)
- [Persyaratan Sistem](#persyaratan-sistem)
- [Cara Menjalankan Proyek](#cara-menjalankan-proyek)
- [Cara Install APK (Debug)](#cara-install-apk-debug)
- [Library & Dependensi](#library--dependensi)
- [Permissions](#permissions)
- [Struktur Package](#struktur-package)

---

## Tentang Proyek

**TP2** adalah aplikasi Android yang dirancang menyerupai antarmuka Instagram. Proyek ini dibuat menggunakan **Java** dengan **Android SDK**, memanfaatkan komponen UI modern seperti `RecyclerView`, `CardView`, dan `CircleImageView`. Data bersifat statis (mock data) yang disimpan secara in-memory melalui kelas `DataStorage`.

| Info | Detail |
|------|--------|
| **Nama Aplikasi** | TP2 |
| **Package** | `com.example.tp2` |
| **Versi** | 1.0 |
| **Bahasa** | Java |
| **Min SDK** | API 24 (Android 7.0 Nougat) |
| **Target SDK** | API 36 (Android 15) |
| **Build Tool** | Gradle |

---

## Fitur Utama

- **Home Feed** — Menampilkan daftar post dari berbagai pengguna dalam format feed scrollable. Setiap item menampilkan foto profil, username, gambar post, dan caption.
- **Profile Page** — Halaman profil lengkap dengan foto, nama, jumlah followers/following/post, grid foto, dan highlight.
- **Highlights** — Row horizontal berisi highlight album milik pengguna di halaman profil.
- **Tambah Post** — Fitur untuk memilih gambar dari galeri dan menambahkan caption untuk memposting konten baru.
- **Detail Feed** — Halaman detail untuk melihat sebuah post secara penuh beserta captionnya.
- **Bottom Navigation** — Navigasi bawah layar dengan tombol Home, Search, Add Post, Reels, dan Profile.

---

## Screenshot

### Halaman Home
![alt text](image.png)
```
[Screenshot Home Feed]
```

### Halaman Profile
![alt text](image-1.png)
```
[Screenshot Profile]
```

### Tambah Post
![alt text](image-2.png)
```
[Screenshot Add Post]
```

### Detail Feed
![alt text](image-3.png)
```
[Screenshot Detail Feed]
```

---

## Arsitektur & Struktur Proyek

Proyek menggunakan pola **Activity + Adapter** standar Android tanpa ViewModel/Repository layer. Data dikelola secara terpusat oleh kelas `DataStorage`.

```
app/
└── src/
    └── main/
        ├── java/com/example/tp2/
        │   ├── HomeActivity.java          # Launcher utama, menampilkan feed & story bar
        │   ├── ProfileActivity.java       # Halaman profil pengguna
        │   ├── AddPostActivity.java       # Form tambah postingan baru
        │   ├── DetailFeedActivity.java    # Detail sebuah post
        │   ├── StoryActivity.java         # Viewer story fullscreen
        │   │
        │   ├── HomeFeedAdapter.java       # RecyclerView adapter untuk feed (2 tipe view)
        │   ├── StoryBarAdapter.java       # RecyclerView adapter untuk story bar
        │   ├── HighlightAdapter.java      # RecyclerView adapter untuk highlights
        │   ├── ProfileGridAdapter.java    # RecyclerView adapter untuk grid foto profil
        │   │
        │   ├── ModelPost.java             # Model data post (Parcelable)
        │   └── DataStorage.java           # Penyimpanan data statis (mock data)
        │
        └── res/
            ├── layout/
            │   ├── activity_home.xml
            │   ├── activity_profile.xml
            │   ├── activity_add_post.xml
            │   ├── activity_detail_feed.xml
            │   ├── activity_detail_story.xml
            │   ├── item_home_feed.xml
            │   ├── item_home_story.xml
            │   ├── item_highlight.xml
            │   ├── item_profile_grid.xml
            │   └── item_story_bar.xml
            │
            └── drawable/
                ├── feed1.png ~ feed10.png       # Foto feed
                ├── foto_profil.png ~ foto_profil10.png  # Foto profil
                ├── hl1.png ~ hl26.png           # Gambar highlight
                └── (ikon & aset lainnya)
```

---

## Alur Navigasi

```
HomeActivity  ──────────────────────────────────────────────┐
    │                                                        │
    ├── [Klik story] ──────────────────► StoryActivity      │
    │                                                        │
    ├── [Klik foto feed] ─────────────► DetailFeedActivity  │
    │                                                        │
    ├── [Klik btn_nav_profile] ──────► ProfileActivity      │
    │         ▲                              │               │
    │         └──────── [Klik Home] ─────────┘              │
    │                                                        │
    └── [Klik btn_nav_add_post] ─────► AddPostActivity      │
              │                              │               │
              └──────── [Submit/Back] ────────┘              │
```

**HomeActivity** adalah launcher utama (`MAIN` + `LAUNCHER`). Semua Activity lain tidak diekspor (hanya dapat dibuka dari dalam aplikasi).

---

## Komponen Teknis

### Activities

| Activity | Deskripsi |
|----------|-----------|
| `HomeActivity` | Launcher utama. Inisialisasi `DataStorage`, menampilkan story bar dan feed melalui dua `RecyclerView`. |
| `ProfileActivity` | Menampilkan data profil, grid foto, dan highlights. Mendukung `onNewIntent` untuk refresh post count. |
| `AddPostActivity` | Menggunakan `ActivityResultLauncher` untuk membuka galeri foto, lalu menyimpan post baru ke `DataStorage`. |
| `DetailFeedActivity` | Menerima data post via `Intent` (Parcelable), menampilkan gambar dan caption secara fullscreen. |
| `StoryActivity` | Menerima data story via `Intent`, menampilkan gambar fullscreen dengan tombol close. |

### Adapters

| Adapter | RecyclerView | Keterangan |
|---------|-------------|------------|
| `HomeFeedAdapter` | `rv_home_feed` | Mendukung **2 tipe ViewHolder**: tipe 0 = story bar, tipe 1 = item feed |
| `StoryBarAdapter` | `rv_story_bar` | Menampilkan daftar avatar story horizontal |
| `HighlightAdapter` | `rv_highlight` | Menampilkan thumbnail highlight horizontal |
| `ProfileGridAdapter` | `rv_profile_grid` | Grid 3 kolom foto profil |

### Model Data

**`ModelPost`** — Model utama yang mengimplementasikan `Parcelable` (dapat dikirim antar Activity via Intent).

Field yang dimiliki:
- `username` — Nama pengguna
- `fullName` — Nama lengkap
- `imageResId` — Resource ID gambar post (drawable)
- `imageUri` — URI gambar (untuk foto dari galeri)
- `profileResId` — Resource ID foto profil
- `caption` — Teks caption post
- `followers`, `following`, `followedBy` — Data sosial pengguna

### DataStorage

Kelas singleton statis yang menginisialisasi semua data dummy saat pertama kali diakses:

- `initHomeFeedIfEmpty()` — Data feed untuk halaman Home
- `initProfileFeedIfEmpty()` — Data grid foto profil
- `initHighlightIfEmpty()` — Data highlight album
- `initUserFeedsIfEmpty()` — Data feed per pengguna
- `initUserHighlightsIfEmpty()` — Data highlight per pengguna

---

## Persyaratan Sistem

### Untuk Menjalankan Aplikasi (Pengguna)
- Perangkat Android dengan **Android 7.0 (API 24)** atau lebih baru
- Izin akses galeri foto (diminta saat pertama kali membuka fitur tambah post)

### Untuk Development (Developer)
- **Android Studio** Hedgehog (2023.1.1) atau lebih baru
- **JDK 17** atau lebih baru
- **Gradle 9.2.1**
- **Android SDK** dengan Build Tools untuk API 36

---

## Cara Menjalankan Proyek

### 1. Clone / Buka Proyek

```bash
# Jika menggunakan Git
git clone <url-repository>
cd <nama-folder>
```

Atau buka folder proyek langsung di Android Studio:
> **File → Open → Pilih folder root proyek**

### 2. Sinkronisasi Gradle

Setelah proyek terbuka, Android Studio akan otomatis mendeteksi `build.gradle`. Klik **"Sync Now"** jika muncul notifikasi di bagian atas editor.

### 3. Siapkan Emulator atau Perangkat Fisik

**Menggunakan Emulator:**
> **Tools → Device Manager → Create Device → Pilih spesifikasi → Finish**

Pastikan API Level emulator minimal **24**.

**Menggunakan Perangkat Fisik:**
1. Aktifkan **Developer Options** di perangkat
2. Aktifkan **USB Debugging**
3. Hubungkan ke komputer via USB
4. Pilih perangkat di dropdown Run target

### 4. Build & Run

Tekan tombol **▶ Run** (atau `Shift + F10`), lalu pilih target perangkat/emulator.

---

## Cara Install APK (Debug)

File APK debug sudah tersedia di:

```
build/intermediates/apk/debug/app-debug.apk
```

### Install via ADB

```bash
adb install build/intermediates/apk/debug/app-debug.apk
```

### Install Manual di Perangkat

1. Salin file `app-debug.apk` ke perangkat Android
2. Buka File Manager di perangkat
3. Ketuk file APK
4. Izinkan instalasi dari sumber tidak dikenal jika diminta
5. Ketuk **Install**

> **Catatan:** APK ini adalah build **debug** (`android:debuggable="true"` dan `android:testOnly="true"`). Tidak untuk distribusi produksi.

---

## Library & Dependensi

| Library | Versi | Kegunaan |
|---------|-------|----------|
| `de.hdodenhof:circleimageview` | 3.1.0 | Menampilkan foto profil berbentuk lingkaran |
| `androidx.cardview:cardview` | 1.0.0 | Layout kartu dengan sudut melengkung |
| `androidx.recyclerview:recyclerview` | — | Daftar item scrollable (feed, grid, story) |
| `androidx.core:core` | 1.18.0 | Backward compatibility Android |
| `androidx.lifecycle:lifecycle-*` | 2.6.2 | Manajemen siklus hidup Activity |
| `androidx.startup:startup-runtime` | 1.1.1 | Inisialisasi komponen saat app start |
| `androidx.profileinstaller` | 1.4.0 | Optimasi performa profil startup |

---

## Permissions

Aplikasi meminta izin berikut:

| Permission | Kegunaan |
|-----------|----------|
| `READ_MEDIA_IMAGES` | Mengakses galeri foto (Android 13+) |
| `READ_EXTERNAL_STORAGE` | Mengakses penyimpanan foto (Android ≤ 12) |

Izin ini digunakan oleh fitur **Tambah Post** untuk memilih gambar dari galeri perangkat.

---

## Struktur Package

```
com.example.tp2
├── Activities
│   ├── HomeActivity        ← Entry point aplikasi
│   ├── ProfileActivity     ← Halaman profil
│   ├── AddPostActivity     ← Tambah postingan
│   ├── DetailFeedActivity  ← Detail post
│   └── StoryActivity       ← Viewer story
│
├── Adapters
│   ├── HomeFeedAdapter     ← Feed utama (story + post)
│   ├── StoryBarAdapter     ← Bar story horizontal
│   ├── HighlightAdapter    ← Highlight album
│   └── ProfileGridAdapter  ← Grid foto profil
│
└── Data
    ├── ModelPost           ← Model data (Parcelable)
    └── DataStorage         ← Mock data terpusat
```

---

## Catatan Pengembangan

- Semua data bersifat **statis/mock** — tidak ada koneksi ke backend atau database eksternal.
- Post baru yang ditambahkan via `AddPostActivity` hanya tersimpan **selama sesi aplikasi berjalan** (in-memory), tidak persisten setelah aplikasi ditutup.
- Tema aplikasi menggunakan `Theme.TP2` yang di-extend dari `MaterialComponents`.

---

> Dibuat sebagai bagian dari Tugas Praktikum 2 — Pemrograman Mobile Android
