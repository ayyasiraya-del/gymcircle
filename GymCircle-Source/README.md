# GymCircle 🏋️

Aplikasi Android untuk **mencatat progres gym, mengikuti metode latihan, dan membangun circle sosial** dalam satu tempat. Dibuat dengan Expo SDK 58, React Native, TypeScript, Expo Router, Zustand, dan Supabase.

> **Mode demo langsung jalan tanpa backend.** Semua fitur latihan, feed, foto, teman, dan komentar tetap bisa dicoba di HP. Fitur sosial multi-user aktif setelah kamu menghubungkan project Supabase.

---

## Daftar isi

- [Fitur](#fitur)
- [Mode demo vs cloud](#mode-demo-vs-cloud)
- [Jalan cepat di Expo Go](#jalan-cepat-di-expo-go)
- [Aktifkan fitur sosial dengan Supabase](#aktifkan-fitur-sosial-dengan-supabase)
- [Build APK dengan EAS](#build-apk-dengan-eas)
- [Build APK secara lokal (opsional)](#build-apk-secara-lokal-opsional)
- [Struktur project](#struktur-project)
- [Script](#script)
- [Troubleshooting](#troubleshooting)
- [Catatan privasi](#catatan-privasi)

---

## Fitur

### Latihan & progres

- **5 program gym bawaan** yang bisa langsung dipakai:
  - Push Pull Legs (6 hari)
  - Upper Lower (4 hari)
  - Full Body 3x (3 hari)
  - Bro Split (5 hari)
  - Home Dumbbell (3 hari)
- Detail program: tujuan, level, estimasi durasi, prinsip latihan, pembagian hari, dan daftar gerakan.
- **Sesi latihan aktif** dengan:
  - log berat × reps per set,
  - centang set selesai,
  - tambah/hapus set,
  - timer sesi berjalan,
  - timer istirahat 60/90/120/180 detik yang diingat,
  - deteksi **personal record** saat set memecahkan rekor,
  - catatan sesi,
  - haptic feedback.
- **Mulai cepat** untuk latihan bebas tanpa program.
- **Foto sesi opsional** saat menyelesaikan latihan (kamera/galeri) dan tampil di riwayat sesi.
- **Feed otomatis**: setiap sesi yang disimpan langsung menjadi postingan feed berisi foto + ringkasan.
- Riwayat sesi dan total volume. Sesi bisa dihapus dari daftar atau dari layar detail, dan postingan feed otomatisnya ikut terhapus.
- **Detail sesi** berisi semua gerakan/set, catatan, dan foto, plus tombol **Ulangi sesi** yang mengisi berat/reps otomatis.
- **Grafik progres**:
  - berat badan (line chart),
  - volume latihan 8 minggu (bar chart),
  - BMI,
  - personal record + estimasi 1RM (rumus Epley),
  - streak mingguan.

### Sosial

- **Feed** dengan foto latihan, caption, sesi latihan yang disertakan, like, komentar, dan share.
- Upload foto dari **galeri atau kamera** (rasio 4:5, dikompres otomatis).
- **Circle**: daftar teman, cari pengguna lain, follow/unfollow, dan papan peringkat volume mingguan.
- **Chat 1-on-1 dan grup** dengan pesan realtime, badge belum dibaca, info anggota grup, dan tambah anggota.
- **Foto profil grup** yang bisa diganti atau dihapus oleh anggota mana pun.
- Profil dengan bio, gym, tujuan, berat/tinggi, pencapaian, dan grid postingan.
- Hapus postingan milik sendiri.
- Mode demo menyimpan semuanya di HP lewat AsyncStorage.

### Tampilan

- Dark theme dengan aksen lime `#C9F75F`.
- UI dan seluruh copy dalam **Bahasa Indonesia**.
- Icon, adaptive icon, favicon, dan splash screen custom.
- Empty state, loading state, error state, dan pull-to-refresh.

---

## Mode demo vs cloud

| | Mode demo | Mode cloud (Supabase) |
|---|---|---|
| Latihan & progres | ✅ tersimpan di HP | ✅ tersimpan di HP |
| Feed, like, komentar | ✅ lokal | ✅ multi-user |
| Upload foto | ✅ tersimpan lokal | ✅ Supabase Storage |
| Teman / follow | ✅ data contoh | ✅ pengguna nyata |
| Papan peringkat | ✅ data contoh + sesi lokal | ✅ teman yang diikuti |
| Login | ❌ tidak perlu | ✅ email + password |

Tanpa file `.env`, app otomatis memakai mode demo. Ini juga yang terjadi jika APK di-build tanpa environment Supabase.

---

## Jalan cepat di Expo Go

### Yang dibutuhkan

- **Node.js 20+** (project ini dites dengan Node 26)
- npm
- Aplikasi **Expo Go** di HP Android (atau iOS)
- HP dan komputer di jaringan Wi-Fi yang sama

### Langkah

```bash
cd gymcircle
npm install
npx expo start
```

Pindai QR code dengan Expo Go (Android) atau Kamera (iOS). Kalau Metro memakai tunnel:

```bash
npx expo start --tunnel
```

Saat pertama dibuka:

1. Isi nama, username, gym, berat/tinggi, tujuan, dan target latihan.
2. Kamu langsung masuk ke feed mode demo.
3. Coba **Latihan → mulai program**, catat set, lalu selesaikan sesi.
4. Buka **Progres** untuk melihat grafik dan PR.
5. Buka **Circle** untuk follow teman contoh dan melihat peringkat.
6. Tekan tombol **+** di feed untuk upload foto dan membuat postingan.

### Cek kualitas project

```bash
npm run typecheck     # TypeScript
npm run doctor        # expo-doctor
npx expo export --platform android   # uji bundling production
```

---

## Aktifkan fitur sosial dengan Supabase

Fitur cloud bersifat **opsional**. Ikuti langkah ini kalau ingin feed dan teman benar-benar multi-user.

### 1. Buat project Supabase

1. Buka [supabase.com](https://supabase.com), daftar/masuk.
2. **New project** → pilih nama, database password, dan region terdekat (misalnya Singapore).
3. Tunggu project selesai provisioning.

### 2. Jalankan schema

1. Buka **SQL Editor** di dashboard Supabase.
2. Salin seluruh isi [`supabase/schema.sql`](supabase/schema.sql).
3. Paste dan klik **Run**.

Schema akan membuat:

- tabel `profiles`, `posts`, `post_likes`, `comments`, `follows`, `workout_sessions`,
- index, trigger otomatis pembuatan profil saat sign-up,
- **Row Level Security** untuk semua tabel,
- storage bucket publik `posts` dan `avatars` beserta policy upload per folder user.

### 2b. Jalankan schema chat

Fitur chat memakai file terpisah supaya bisa ditambahkan ke project yang sudah berjalan:

1. Masih di **SQL Editor**.
2. Salin seluruh isi [`supabase/chat.sql`](supabase/chat.sql).
3. Paste dan klik **Run**.

File ini membuat tabel `conversations`, `conversation_members`, `messages`, RPC untuk membuka chat 1-on-1 / membuat grup / menambah anggota, RLS, dan mengaktifkan **Realtime** untuk pesan baru.

### 2c. Jalankan update foto grup & foto sesi

Kalau database sudah pernah dibuat sebelum fitur foto:

1. Masih di **SQL Editor**.
2. Salin seluruh isi [`supabase/update-group-workout-photo.sql`](supabase/update-group-workout-photo.sql).
3. Paste dan klik **Run**.

File ini menambahkan kolom `conversations.avatar_url`, `workout_sessions.photo_url`, dan RPC `set_group_photo`. Untuk instalasi baru, kolom ini sudah ikut di `schema.sql` dan `chat.sql`.

### 3. Atur konfirmasi email

Untuk pengujian cepat:

1. **Authentication → Providers → Email**.
2. Matikan **Confirm email**.
3. Simpan.

Kalau fitur ini dibiarkan aktif, user harus mengklik link konfirmasi di email sebelum bisa login. App sudah menampilkan pesan yang sesuai.

### 4. Isi environment

```bash
cp .env.example .env
```

Buka `.env`, lalu isi dari **Project Settings → API**:

```dotenv
EXPO_PUBLIC_SUPABASE_URL=https://xxxxxxxxxxxx.supabase.co
EXPO_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOi...
```

> Gunakan **anon/public key**, bukan `service_role`. Anon key memang aman dipakai di client karena akses data dibatasi RLS.

### 5. Restart app

```bash
npx expo start -c
```

Setelah restart:

1. Selesaikan onboarding (jika belum).
2. Kamu diarahkan ke layar **Masuk**.
3. Pilih **Daftar**, buat akun, lalu masuk.
4. Atau pilih **Coba dulu, mode demo** kapan saja.

### Pindah dari demo ke cloud

Di tab **Profil**, ketuk badge **Demo** di kanan atas (atau menu *Masuk ke akun cloud*). Kamu akan diarahkan ke layar masuk.

### Pindah dari cloud ke demo

Di tab **Profil**, ketuk **Akun → Keluar**. Untuk masuk lagi, buka **Profil → Masuk ke akun cloud**.

---

## Build APK dengan EAS

Cara paling mudah menghasilkan APK tanpa memasang Android SDK. Build berjalan di cloud Expo.

### 1. Login / daftar Expo

```bash
npx eas-cli@latest login
```

Kalau belum punya akun, daftar di [expo.dev](https://expo.dev).

### 2. Hubungkan project (sekali saja)

```bash
npx eas-cli@latest build:configure
```

Perintah ini menambahkan `extra.eas.projectId` ke `app.json`. File `eas.json` sudah disiapkan dengan profil **preview** yang menghasilkan **APK**.

### 3. Set environment untuk build cloud (opsional)

`.env` lokal **tidak ikut ter-upload** ke EAS Build. Kalau ingin APK langsung terhubung ke Supabase, daftarkan environment `preview`:

```bash
npx eas-cli@latest env:set \
  --name EXPO_PUBLIC_SUPABASE_URL \
  --value "https://xxxxxxxxxxxx.supabase.co" \
  --environment preview \
  --visibility plaintext

npx eas-cli@latest env:set \
  --name EXPO_PUBLIC_SUPABASE_ANON_KEY \
  --value "eyJhbGciOi..." \
  --environment preview \
  --visibility plaintext
```

Kalau langkah ini dilewati, APK tetap bisa dipasang dan berjalan penuh di **mode demo**.

### 4. Build APK

```bash
npx eas-cli@latest build --platform android --profile preview
```

- EAS akan membuat keystore Android otomatis pada build pertama.
- Setelah selesai, terminal menampilkan link download.
- Buka link tersebut di HP, unduh APK, lalu izinkan **Install unknown apps**.
- Atau buka [expo.dev/builds](https://expo.dev/builds) dan pilih build terbaru.

### 5. (Opsional) Build untuk Play Store

```bash
npx eas-cli@latest build --platform android --profile production
```

Profil `production` menghasilkan **AAB** yang siap diunggah ke Google Play Console.

> Build cloud memakai kuota akun Expo. Untuk pemakaian pribadi, profil `preview` sudah cukup.

---

## Build APK secara lokal (opsional)

Kalau punya Android Studio + JDK 17 + Android SDK:

```bash
npx expo prebuild --platform android
cd android
./gradlew assembleRelease
```

APK ada di:

```text
android/app/build/outputs/apk/release/app-release.apk
```

Catatan:

- Folder `android/` dan `ios/` di-generate oleh Expo (Continuous Native Generation). Jangan diedit manual; ubah konfigurasi di `app.json`.
- Build lokal membutuhkan signing config sendiri untuk distribusi. Untuk kebanyakan orang, **EAS Build lebih mudah**.

---

## Struktur project

```text
gymcircle/
├── app.json                     # konfigurasi Expo, Android package, icon, splash
├── eas.json                     # profil build APK/AAB
├── index.js                     # entry Expo Router + URL polyfill
├── src/
│   ├── app/                     # route Expo Router
│   │   ├── _layout.tsx          # root stack + provider
│   │   ├── index.tsx            # splash & redirect
│   │   ├── onboarding.tsx
│   │   ├── auth.tsx
│   │   ├── (tabs)/
│   │   │   ├── _layout.tsx
│   │   │   ├── feed.tsx
│   │   │   ├── workout.tsx
│   │   │   ├── progress.tsx
│   │   │   ├── circle.tsx
│   │   │   ├── chat.tsx
│   │   │   └── profile.tsx
│   │   ├── program/[id].tsx
│   │   ├── sessions.tsx
│   │   ├── session/[id].tsx
│   │   ├── chat-room/[id].tsx
│   │   ├── group-info/[id].tsx
│   │   ├── new-chat.tsx
│   │   ├── active-workout.tsx
│   │   ├── create-post.tsx
│   │   ├── comments/[postId].tsx
│   │   ├── edit-profile.tsx
│   │   └── add-measurement.tsx
│   ├── components/              # Avatar, PostCard, Charts, dll.
│   ├── data/
│   │   ├── programs.ts          # 5 program + quick workout
│   │   └── seed.ts              # profil & feed demo
│   ├── lib/
│   │   ├── supabase.ts          # client Supabase + deteksi env
│   │   ├── SocialProvider.tsx   # demo/cloud social & auth
│   │   ├── ChatProvider.tsx     # daftar chat, unread, realtime
│   │   ├── chat.ts              # query & subscription chat
│   │   └── utils.ts             # tanggal, volume, 1RM, dll.
│   ├── store/useAppStore.ts     # Zustand + AsyncStorage
│   ├── theme.ts                 # token warna, radius, spacing
│   └── types.ts
├── supabase/
│   ├── schema.sql               # tabel, RLS, trigger, storage
│   ├── chat.sql                 # tabel chat, RPC, realtime
│   └── update-group-workout-photo.sql  # kolom foto grup + foto sesi
├── scripts/generate-assets.py   # generator icon/splash
└── assets/                      # icon, adaptive icon, splash, favicon
```

---

## Script

| Perintah | Fungsi |
|---|---|
| `npm start` | Jalankan Metro / Expo dev server |
| `npm run android` | Buka di emulator/HP Android |
| `npm run ios` | Buka di simulator/HP iOS |
| `npm run typecheck` | Cek TypeScript |
| `npm run doctor` | Cek kesehatan dependency & konfigurasi Expo |
| `npx expo export --platform android` | Uji bundling production |
| `npx eas-cli@latest build -p android --profile preview` | Build APK di cloud |

---

## Troubleshooting

### Expo Go tidak bisa membuka project

- Pastikan HP dan komputer satu Wi-Fi.
- Coba `npx expo start --tunnel`.
- Update Expo Go dari Play Store.
- Hapus cache: `npx expo start -c`.

### “Cloud belum diaktifkan” muncul terus

- Pastikan file `.env` ada di root project (bukan di `src/`).
- Variabel harus diawali `EXPO_PUBLIC_`.
- Restart dengan `npx expo start -c`; env dibaca saat Metro start.
- Cek tidak ada spasi atau tanda kutip aneh di `.env`.

### Login berhasil tapi feed error / kosong

- Pastikan `supabase/schema.sql` sudah dijalankan sampai selesai.
- Cek **Authentication → Users**; user harus ada.
- Cek **Table Editor → profiles**; trigger harus otomatis membuat satu baris.
- Buka **Logs → API** untuk melihat error RLS.
- Pastikan RLS aktif dan policy dari schema tidak dihapus.

### Upload foto gagal

- Pastikan bucket `posts` dan `avatars` ada di **Storage**.
- Jalankan ulang bagian storage pada `supabase/schema.sql`.
- Pastikan user sudah login (policy upload membutuhkan `auth.uid()`).
- Di mode demo, foto disalin ke dokumen app; kalau file hilang, postingan lama bisa kehilangan gambarnya.

### APK ter-build tapi tetap mode demo

- Environment `preview` belum diisi di EAS.
- Jalankan `eas env:set` seperti di atas, lalu build ulang.
- Environment variables di-embed saat build, jadi wajib build ulang setelah diubah.

### `npm install` gagal karena peer dependency

Project ini memakai React 19.3.0 dan `react-dom` 19.3.0. Gunakan npm yang cukup baru (npm 10+). Kalau muncul `ERESOLVE`, hapus `node_modules` dan `package-lock.json`, lalu `npm install` ulang.

### Build EAS gagal karena JDK/keystore

EAS mengurus JDK dan keystore. Kalau build pertama meminta konfirmasi generate keystore, pilih **Yes**. Untuk build lokal, gunakan JDK 17.

---

## Catatan privasi

- Data latihan, berat badan, dan feed mode demo disimpan **lokal di HP** (AsyncStorage + dokumen app).
- Di mode cloud, profil, postingan, komentar, follow, dan ringkasan sesi latihan disimpan di Supabase.
- Bucket `posts` dan `avatars` bersifat publik agar gambar bisa tampil di feed. Jangan unggah dokumen pribadi.
- Berat/tinggi hanya dipakai untuk BMI dan tidak otomatis tampil di postingan.
- Aplikasi ini bukan pengganti saran medis. Hentikan latihan jika terasa nyeri tajam dan konsultasikan dengan profesional.

---

## Lisensi

MIT. Silakan pakai, ubah, dan kembangkan untuk kebutuhanmu.

**Train · Share · Grow.**
