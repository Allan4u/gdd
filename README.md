# 🎮 Tile Blast — Dokumen Desain Game (GDD)

---

## 📋 Ringkasan

| Field | Detail |
|---|---|
| **Judul** | Tile Blast |
| **Genre** | Puzzle / Strategi |
| **Platform** | Android, iOS, Web |
| **Engine** | React Native (Expo SDK 52) |
| **Target Pemain** | Gamer kasual, usia 8+ |
| **Monetisasi** | Gratis (TBD: Iklan / Premium) |
| **Package ID** | `com.allan.tileblast` |
| **Versi** | 1.0.0 |

---

## 🎯 Konsep Game

Tile Blast adalah game puzzle penempatan blok yang terinspirasi dari **1010!** dan **Block Puzzle**. Pemain diberikan set potongan puzzle acak yang harus di-drag-and-drop ke dalam grid. Ketika seluruh **baris** atau **kolom** terisi penuh, baris/kolom tersebut akan dihapus — menghasilkan poin. Game berakhir ketika tidak ada potongan tersisa yang bisa ditempatkan.

### Inti Permainan
> *"Isi baris, pecahkan combo, dan kejar skor tertinggi."*

---

## 🕹️ Mode Permainan

### 1. Mode Classic
| Properti | Nilai |
|---|---|
| Ukuran Grid | **8 × 8** |
| Jumlah Tangan | **3 potongan** |
| Kesulitan | Standar |
| Deskripsi | Pengalaman puzzle klasik. Grid 8×8 yang seimbang dengan 3 potongan di tangan. |

### 2. Mode Chaos
| Properti | Nilai |
|---|---|
| Ukuran Grid | **10 × 10** |
| Jumlah Tangan | **5 potongan** |
| Kesulitan | Sulit |
| Deskripsi | Grid 10×10 yang lebih besar dengan 5 potongan untuk dikelola. Lebih strategis, lebih kacau. |

---

## 🧩 Potongan (Tetrominoes & Polyominoes)

Semua potongan didefinisikan sebagai matriks biner. Setiap potongan memiliki **bobot distribusi** yang mengontrol frekuensi kemunculan.

### Katalog Potongan

| Bentuk | Nama | Ukuran | Blok | Bobot Spawn | Tampilan |
|---|---|---|---|---|---|
| L | Bentuk-L (8 rotasi) | 3×2 / 2×3 | 4 | 2.0 per rotasi | ⬛⬜⬜ / ⬛⬛⬛ |
| T | Bentuk-T (4 rotasi) | 3×2 / 2×3 | 4 | 1.5 per rotasi | ⬛⬛⬛ / ⬜⬛⬜ |
| S/Z | Bentuk-S/Z (4 rotasi) | 3×2 / 2×3 | 4 | 1.0 per rotasi | ⬜⬛⬛ / ⬛⬛⬜ |
| □ | Kotak 3×3 | 3×3 | 9 | 3.0 | ⬛⬛⬛ / ⬛⬛⬛ / ⬛⬛⬛ |
| ■ | Kotak 2×2 | 2×2 | 4 | 6.0 | ⬛⬛ / ⬛⬛ |
| I | Garis (Horizontal 1×4) | 4×1 | 4 | 2.0 | ⬛⬛⬛⬛ |
| I | Garis (Vertikal 4×1) | 1×4 | 4 | 2.0 | ⬛ / ⬛ / ⬛ / ⬛ |
| — | Garis Pendek (1×3) | 3×1 | 3 | 4.0 | ⬛⬛⬛ |
| \| | Garis Pendek (3×1) | 1×3 | 3 | 4.0 | ⬛ / ⬛ / ⬛ |
| · | Domino (1×2) | 2×1 | 2 | 2.0 | ⬛⬛ |
| · | Domino (2×1) | 1×2 | 2 | 2.0 | ⬛ / ⬛ |

**Total: 24 bentuk potongan unik**

### Sistem Distribusi
Potongan dipilih menggunakan algoritma **random berbobot**. Semakin tinggi `distributionPoints`, semakin sering muncul. Kotak 2×2 (bobot 6.0) paling sering muncul; bentuk S/Z (bobot 1.0) paling jarang.

### Warna Potongan
Setiap potongan diberi **warna acak** dari palet berikut:

| Warna | RGB | Hex | Tampilan |
|---|---|---|---|
| Oranye | (227, 143, 16) | `#E38F10` | 🟠 |
| Merah Tua | (186, 19, 38) | `#BA1326` | 🔴 |
| Hijau | (16, 158, 40) | `#109E28` | 🟢 |
| Biru | (20, 56, 184) | `#1438B8` | 🔵 |
| Ungu | (101, 19, 148) | `#651394` | 🟣 |
| Biru Muda | (31, 165, 222) | `#1FA5DE` | 🔵 |

### Rendering Blok
Setiap blok menampilkan **border 3D beveled** yang dihitung secara dinamis:
- **Border atas**: Lebih terang (highlight)
- **Border kiri**: Sedikit lebih terang
- **Border kanan**: Lebih gelap
- **Border bawah**: Paling gelap (bayangan)

---

## 📏 Mekanik Inti

### 1. Sistem Tangan (Hand)
- Pemain menerima tangan berisi potongan (**3** di Classic, **5** di Chaos)
- Potongan harus ditempatkan **satu per satu** dengan drag-and-drop
- Ketika **semua potongan** di tangan sudah ditempatkan, **tangan acak baru** dibagikan
- Potongan **tidak bisa diputar** — harus ditempatkan apa adanya

### 2. Aturan Penempatan
- Potongan hanya bisa ditempatkan di **sel grid yang kosong**
- Seluruh potongan harus muat dalam batas grid
- **Zona drop yang valid** ditandai saat menyeret potongan
- Penempatan tidak valid akan mengembalikan potongan ke tangan

### 3. Penghancuran Baris/Kolom
- Sebuah **baris** dihapus ketika semua sel di baris tersebut terisi
- Sebuah **kolom** dihapus ketika semua sel di kolom tersebut terisi
- Beberapa baris dan/atau kolom bisa dihapus secara bersamaan
- Sel yang dihapus menjadi kosong dan siap diisi kembali

### 4. Preview Hover
Saat menyeret potongan di atas grid:
- **Penempatan valid**: Blok di-preview dengan **opacity 30%**
- **Preview penghancuran baris**: Jika penempatan akan menyelesaikan baris, sel-sel tersebut **bersinar dengan warna potongan** (efek elevated)
- **Border grid**: Berubah warna sesuai potongan yang sedang diseret

### 5. Game Over
Game berakhir ketika **tidak ada potongan di tangan** yang bisa ditempatkan di manapun di papan. Pengecekan otomatis dilakukan setelah setiap penempatan.

---

## 📊 Sistem Skor

### Skor Dasar
- **Menempatkan potongan**: `+1` poin per blok dalam potongan
  - Contoh: Kotak 3×3 menghasilkan **+9 poin**

### Bonus Penghancuran Baris
```
Bonus Baris = (baris_dihancurkan × panjang_papan × 10) × pengali_combo
```

| Baris Dihancurkan | Classic (8×8) Dasar | Chaos (10×10) Dasar |
|---|---|---|
| 1 baris | 80 poin | 100 poin |
| 2 baris | 160 poin | 200 poin |
| 3 baris | 240 poin | 300 poin |
| 4+ baris | 320+ poin | 400+ poin |

### Sistem Combo
- **Combo** melacak giliran berturut-turut yang menghancurkan baris
- Jika **2 penempatan berturut-turut** menghancurkan baris, combo bertambah
- Combo **direset** setelah **2 penempatan** tanpa menghancurkan baris apapun
- Combo berperan sebagai **pengali skor** pada bonus penghancuran baris:

| Level Combo | Pengali | Feedback Visual |
|---|---|---|
| ×1 | 1× | Suara pecah |
| ×2 | 2× | Suara combo + teks "COMBO x2!" |
| ×3 | 3× | Suara combo + teks |
| ×4+ | 4×+ | Suara combo + suara "JUICINESS" + teks |

---

## 🔊 Sistem Audio

### Efek Suara
| Suara | Pemicu | File |
|---|---|---|
| `PLACE` | Potongan ditempatkan di papan | `place.mp3` |
| `BREAK` | Penghancuran baris tunggal (tanpa combo) | `break.mp3` |
| `COMBO1-5` | Level combo 2-6+ | `combo1.mp3` — `combo5.mp3` |
| `JUICINESS` | Combo ×4+ atau 3+ baris dihancurkan sekaligus | `Juiciness.mp3` |
| `GAMEOVER` | Game over terpicu | `gameover.mp3` |

### Kontrol Volume
- Volume dikontrol melalui `volumeAtom` global (state Jotai)
- Dapat disesuaikan dari menu Opsi

### Feedback Haptic
- Getaran **impact ringan** di perangkat native saat potongan berhasil ditempatkan
- Tidak ada haptic di web

---

## 🎨 Desain Visual

### Tema
- **Tema luar angkasa gelap** dengan latar belakang hitam
- Blok berwarna-warni dengan efek kedalaman 3D (beveled)
- Efek bersinar pada preview penghancuran baris

### Tipografi
| Font | Penggunaan |
|---|---|
| `Silkscreen` | Skor, teks UI, tombol |
| `SilkscreenBold` | Judul, teks combo, header pause/game over |

### Partikel Latar Belakang
- Bentuk potongan mengambang dengan animasi di latar belakang (hanya di menu)
- 8 partikel dengan efek fade-in/fade-out dan gerakan melayang
- Dinonaktifkan saat bermain untuk performa

### Feedback UI
- **Guncangan layar** saat baris dihancurkan (intensitas sesuai jumlah baris)
- **Ledakan partikel** di sepanjang baris/kolom yang dihapus
- **Overlay combo** dengan animasi masuk/keluar ("COMBO x3!")
- **Warna border grid** mengikuti warna potongan yang diseret

---

## 🖥️ Alur UI / UX

```
┌─────────────────┐
│   MENU UTAMA     │
│                  │
│  [Mode Classic]  │
│  [Mode Chaos]    │
│  [Skor Tertinggi]│
│  [Opsi]          │
└───────┬──────────┘
        │
   ┌────▼────┐
   │  GAME   │
   │         │
   │  HUD    │ ← Skor, Combo, Statistik
   │  GRID   │ ← 8×8 atau 10×10
   │  TANGAN │ ← 3 atau 5 potongan
   │         │
   │  [||]   │ ← Tombol Pause
   └────┬────┘
        │
   ┌────▼─────────┐     ┌──────────────┐
   │    DIJEDA     │     │  GAME OVER   │
   │               │     │              │
   │  [Lanjut]     │     │ Skor Akhir   │
   │  [Opsi]       │     │ [Kembali]    │
   │  [Keluar]     │     └──────────────┘
   └───────────────┘
```

### Menu Utama
- Logo game dengan animasi bounce-in
- Tombol pilihan mode dengan gradien dan teks deskripsi
- Partikel potongan mengambang di latar belakang
- Footer versi/kredit

### HUD Dalam Game
- **Atas**: Skor saat ini (angka animasi), indikator skor tertinggi
- **Tengah**: Grid permainan
- **Bawah**: Potongan tangan (preview yang diperkecil)
- **Kanan atas**: Tombol pause

### Menu Opsi
- Pengatur volume
- Tombol kembali

### Skor Tertinggi
- Papan peringkat per-mode
- Disimpan secara lokal via AsyncStorage
- Menampilkan skor + tanggal

---

## 💾 Penyimpanan Data

### Teknologi
- **AsyncStorage** (React Native) untuk penyimpanan lokal
- Setiap sesi game membuat entri **UUID** unik
- Skor diperbarui secara real-time selama bermain

### Skema Data
```typescript
interface HighScore {
    score: number;      // Skor akhir
    date: number;       // Timestamp (milidetik)
    type: GameModeType; // "Classic" atau "Chaos"
}
```

### Kunci Penyimpanan
- `HIGH_SCORES` → Array UUID yang mengarah ke entri skor individual
- Setiap UUID → Objek `HighScore` yang di-serialisasi JSON

---

## 📱 Desain Responsif

### Ukuran Grid Dinamis
```typescript
ukuranBlok = min(
    (lebarLayar - 40) / panjangPapan,    // Sesuai lebar
    (tinggiLayar - 300) / panjangPapan,  // Sesuai tinggi
    46                                    // Batas maksimum
)
```

- Grid otomatis menyesuaikan untuk ukuran layar apapun
- Potongan tangan menyesuaikan secara proporsional (50% dari ukuran blok grid)
- Mendengarkan event resize layar (perubahan orientasi, dll.)

### Resolusi yang Didukung
- HP: lebar 320px — 430px
- Tablet: lebar 768px — 1024px
- Desktop: lebar 1024px+

---

## ⚙️ Arsitektur Teknis

### Stack Teknologi
| Lapisan | Teknologi |
|---|---|
| Framework | React Native 0.76 + Expo SDK 52 |
| Routing | Expo Router |
| State | Jotai (atoms) + Reanimated SharedValues |
| Animasi | React Native Reanimated 3 |
| Drag & Drop | `@mgcrea/react-native-dnd` |
| Haptic | `expo-haptics` |
| Audio | `expo-av` |
| Penyimpanan | `@react-native-async-storage/async-storage` |
| Kriptografi | `expo-crypto` (pembuatan UUID) |

### Modul Utama
| File | Tanggung Jawab |
|---|---|
| `app/index.tsx` | Entry point, pemuatan font, routing layar |
| `components/game/Game.tsx` | Game loop inti, skor, penanganan drag-and-drop |
| `components/game/BlockGrid.tsx` | Rendering grid dengan style blok animasi |
| `components/game/HandPieces.tsx` | Tampilan tangan dengan potongan yang bisa diseret |
| `components/game/GameHud.tsx` | HUD skor dan combo |
| `constants/Board.tsx` | Logika papan, deteksi baris, preview hover |
| `constants/Piece.tsx` | Definisi potongan, warna, rendering blok |
| `constants/Hand.tsx` | Pembuatan tangan |
| `constants/Color.tsx` | Utilitas warna (HSL, RGB, konversi hex) |
| `constants/Audio.ts` | Manajer audio (pola singleton) |
| `constants/Storage.tsx` | CRUD skor tertinggi dengan AsyncStorage |

### Optimisasi Performa
- Style blok grid dihitung di **UI thread** (worklets)
- Deteksi tabrakan berjalan sebagai worklet (tanpa panggilan JS bridge)
- Partikel latar belakang dinonaktifkan saat bermain
- Penghitungan ulang layout hanya saat state berubah (bukan per-frame)
- React.memo pada komponen partikel

---

## 🗺️ Peta Jalan Pengembangan

### v1.1 — Peningkatan
- [ ] Mode Tantangan Harian (puzzle dengan seed)
- [ ] Undo langkah terakhir (penggunaan terbatas)
- [ ] Opsi rotasi potongan
- [ ] Sistem pencapaian (achievement)
- [ ] Kustomisasi tema (warna, latar belakang)

### v1.2 — Sosial
- [ ] Papan peringkat online (Firebase)
- [ ] Bagikan skor sebagai gambar
- [ ] Tantangan antar teman

### v1.3 — Monetisasi
- [ ] Iklan berhadiah untuk undo ekstra
- [ ] Tema kosmetik premium
- [ ] Hapus iklan (pembelian sekali)

### v2.0 — Mode Baru
- [ ] Mode Waktu (kejar waktu)
- [ ] Mode Zen (tanpa game over, relaksasi)
- [ ] Mode Battle (kompetitif 1v1)
- [ ] Mode Puzzle (tantangan yang sudah ditentukan)

---

## 📄 Kredit

| Peran | Nama |
|---|---|
| Pengembang | Allan |
| Engine | React Native + Expo |
| Font | Silkscreen oleh Jason Kottke |

---

*Versi Dokumen: 1.0 — Terakhir Diperbarui: 20 April 2026*
