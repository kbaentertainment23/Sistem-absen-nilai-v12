# Implementasi Bottom Action Bar & Mode Pengisian Presensi Praktis pada Absensi Harian

Rencana ini merealisasikan solusi menyeluruh untuk mempermudah guru saat mengisi presensi siswa: menghilangkan kelelahan scroll berulang, menampilkan ringkasan hadir/sakit/izin/alfa secara real-time di bawah layar, serta memastikan tombol **"Simpan & Sinkronkan"** selalu terlihat dan siap ditekan kapan saja tanpa menutupi data siswa.

---

## User Review & Critical Decisions

> [!IMPORTANT]
> Berdasarkan jawaban klarifikasi pengguna:
> 1. **Model Antarmuka Terpilih**: **Bar Simpan tetap di bawah layar (*Sticky Bottom Action Bar*)** yang dilengkapi ringkasan status presensi (Total Hadir, Sakit, Izin, Alfa, Terlambat).
> 2. **Metode Aktivasi**: **Aktif otomatis** begitu guru mulai mengisi atau mengubah status kehadiran siswa (atau saat terdeteksi perubahan belum tersimpan / sedang mengisi tabel).

- **Keuntungan Utama**:
  - Tabel siswa tidak tertutupi oleh elemen yang melayang di tengah layar.
  - Guru dapat leluasa mengisi absensi dari siswa nomor 1 hingga 30+ tanpa khawatir tombol simpan tertinggal di atas.
  - Terdapat ringkasan langsung di bagian bawah layar sehingga guru langsung mengetahui jumlah murid yang hadir, sakit, izin, atau alfa tanpa harus menggulir ke panel atas.

---

## 1. Overview & Core Concept

- **Apa yang Dibuat**: Komponen bilah aksi bawah tetap (*Sticky Bottom Bar*) yang elegan dan responsif pada `AttendanceTab.tsx`.
- **Target Pengguna**: Guru dan wali kelas yang sedang mengisi absensi harian kelas.
- **Nilai Manfaat**: 
  - Waktu pengisian presensi menjadi jauh lebih singkat dan efisien.
  - Menghilangkan friksi scroll bolak-balik antara tabel dan tombol simpan di atas.
  - Guru mendapat kepastian langsung mengenai status tersimpan / belum tersimpan.

---

## 2. User Experience & Visual Design

### Alur Interaksi Pengguna (User Flow)
1. **Membuka Halaman Absensi**:
   - Guru memilih tanggal dan kelas seperti biasa.
2. **Saat Guru Mengisi Presensi**:
   - Ketika guru mulai mengubah status presensi (misal klik "Hadir", "Sakit", "Izin", "Alfa", atau tombol aksi cepat "Tandai Semua Hadir"), bilah aksi bawah (*Sticky Bottom Bar*) muncul secara mulus dengan transisi `slide-in-from-bottom`.
3. **Isi Bilah Bawah (*Bottom Bar*)**:
   - **Kiri**: Ringkasan status langsung (Badge counter: Hadir, Sakit, Izin, Alfa, Terlambat) dan indikator status belum disimpan (*Unsaved changes*).
   - **Kanan**: Tombol utama **"Simpan & Sinkronkan"** berukuran nyaman untuk diklik/disentuh, lengkap dengan indikator proses (*Syncing spinner*), serta tombol pintasan cepat untuk kembali ke atas (*Scroll to top*).
4. **Setelah Disimpan**:
   - Tombol berubah menjadi status sukses (*"Tersimpan & Tersinkron!"*) dan memberikan umpan balik visual yang melegakan guru.

### Tata Letak & Spasi Visual
- Diberikan *padding bottom* yang cukup (`pb-28`) pada kontainer tabel agar baris siswa paling bawah tidak tertutup oleh bilah bawah saat digulir sampai ujung.
- Desain menggunakan latar `bg-white/95 dark:bg-slate-900/95 backdrop-blur-md` dengan border halus `border-t border-slate-200` dan bayangan lembut `shadow-2xl`.

---

## 3. Technical Architecture & Component Mapping

```
┌──────────────────────────────────────────────────────────────┐
│                    AttendanceTab.tsx                         │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │ Kalender & Statistik Atas (Bisa diperkecil/collapse)   │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │ Tabel Presensi Siswa (No, Nama, Status H/S/I/A, Ket)   │  │
│  │ (Scroll lancar tanpa div yang menutupi tabel)          │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                              │
│  ══════════════════════════════════════════════════════════  │
│  [STICKY BOTTOM BAR - Terpancang di bagian bawah layar]      │
│  • Ringkasan: [Hadir: 28] [Sakit: 1] [Izin: 0] [Alfa: 1]     │
│  • Tombol: [ 💾 Simpan & Sinkronkan ]                        │
│  ══════════════════════════════════════════════════════════  │
└──────────────────────────────────────────────────────────────┘
```

### Rincian Perubahan Kode di `AttendanceTab.tsx`:
1. **Deteksi Perubahan / Pengisian Presensi (`isDirty`)**:
   - Menambahkan state `isDirty` yang menjadi `true` setiap kali guru mengubah status atau mengklik "Tandai Semua Hadir".
2. **Sticky Bottom Bar Component**:
   - Menambahkan kontainer tetap `fixed bottom-0 left-0 right-0 z-40` yang otomatis aktif saat ada data terisi atau perubahan belum disimpan.
   - Menyertakan ringkasan counter presensi harian (`present`, `sick`, `excused`, `absent`, `lateCount`).
   - Menyertakan tombol **"Simpan & Sinkronkan"** dengan event handler `handleSave` yang sama.
   - Mereset `isDirty` menjadi `false` setelah penyimpanan berhasil.
3. **Penyelarasan Spasi Halaman**:
   - Menambahkan `pb-24 sm:pb-28` pada kontainer tabel agar baris siswa paling akhir tidak terhalang.
