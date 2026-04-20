# 🎮 Tile Blast — Dokumen Desain Game (GDD)

---

## 📋 Ringkasan

| Field | Detail |
|---|---|
| **Judul** | Tile Blast |
| **Genre** | Puzzle / Strategi |
| **Platform** | Android |
| **Engine** | Native Java (Android SDK) |
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
Setiap blok menampilkan **border 3D beveled** yang dihitung secara dinamis langsung pada Canvas:
- **Border atas**: Lebih terang (highlight)
- **Border kiri**: Sedikit lebih terang
- **Border kanan**: Lebih gelap
- **Border bawah**: Paling gelap (bayangan)

---

## 📏 Mekanik Inti

### 1. Sistem Tangan (Hand)
- Pemain menerima tangan berisi potongan (**3** di Classic, **5** di Chaos)
- Potongan harus ditempatkan **satu per satu** dengan menahan layar (Touch Event)
- Ketika **semua potongan** di tangan sudah ditempatkan, **tangan acak baru** dibagikan
- Potongan **tidak bisa diputar** — harus ditempatkan apa adanya

### 2. Aturan Penempatan
- Potongan hanya bisa ditempatkan di **sel grid yang kosong**
- Seluruh potongan harus muat dalam batas grid
- **Zona drop yang valid** ditandai saat menyeret potongan
- Penempatan tidak valid akan mengembalikan potongan ke tangan bebas

### 3. Penghancuran Baris/Kolom
- Sebuah **baris** dihapus ketika semua sel di baris tersebut terisi
- Sebuah **kolom** dihapus ketika semua sel di kolom tersebut terisi
- Beberapa baris dan/atau kolom bisa dihapus secara bersamaan
- Sel yang dihapus menjadi kosong dan siap diisi kembali

### 4. Preview Hover
Saat menyeret potongan di atas grid:
- **Penempatan valid**: Blok di-preview langsung menggunakan perhitungan posisi dengan **opacity 30%**
- **Preview penghancuran baris**: Jika penempatan akan menyelesaikan baris, sel-sel tersebut **bersinar dengan warna potongan** (efek elevated)
- **Border grid**: Berubah warna meniru pinggiran potongan yang sedang diseret

### 5. Game Over
Game berakhir ketika **tidak ada potongan di tangan** yang bisa ditempatkan di manapun di papan. Pengecekan otomatis dilakukan setelah setiap penempatan blok sukses.

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
| `BREAK` | Penghancuran baris tunggal (tanpa combo) | `break_.mp3` |
| `COMBO1-5` | Level combo 2-6+ | `combo1.mp3` — `combo5.mp3` |
| `JUICINESS` | Combo ×4+ atau 3+ baris dihancurkan sekaligus | `Juiciness.mp3` |
| `GAMEOVER` | Game over terpicu | `gameover.mp3` |

### Kontrol Volume
- Volume dikontrol lalu disetting secara internal dengan sistem `SoundPool` volume control
- Disimpan menggunakan `SharedPreferences`

### Feedback Haptic
- Menggunakan `Vibrator` Service bawaan Android
- Getaran **impact ringan (30 milidetik)** di perangkat saat potongan berhasil ditempatkan. Opsi Amplitude standar bila Native Support Vibrator OS >= versi O API (Build.VERSION_CODES.O).

---

## 🎨 Desain Visual

### Tema
- **Tema luar angkasa gelap** dengan latar belakang murni warna `#000000`
- Blok berwarna-warni dengan efek kedalaman 3D (beveled Canvas rendering)
- Efek bersinar pada preview penghancuran baris (merubah alpha / transparansi di canvas)

### Tipografi
| Font | Penggunaan |
|---|---|
| `Silkscreen` | Teks keterangan dan High Scores |
| `SilkscreenBold` | Judul, skor berjalan, menu utama, overlay pause |

### Feedback UI
- **Guncangan layar (Screen Shake)** saat baris dihancurkan (intensitas shake mengikuti skala baris + translasi sumbu x & y)
- **Overlay combo** dengan animasi perlahan menghilang (fade-out di render cycle)
- **Warna border grid** mengikuti warna keping yang disentuh.

---

## 🖥️ Alur UI / UX

```
┌─────────────────┐
│   MENU UTAMA     │
│  (MainActivity)  │
│                  │
│  [Mode Classic]  │
│  [Mode Chaos]    │
│  [Skor Tertinggi]│
└───────┬──────────┘
        │
   ┌────▼─────────────┐
   │  GAME            │
   │ (GameActivity)   │
   │                  │
   │  HUD             │ ← Skor, Combo, Statistik
   │  GRID (Canvas)   │ ← 8×8 atau 10×10
   │  TANGAN          │ ← 3 atau 5 potongan
   │                  │
   │  [II]            │ ← Tombol Pause (Klik via Koordinat)
   └────┬─────────────┘
        │
   ┌────▼─────────┐     ┌──────────────┐
   │    DIJEDA     │     │  GAME OVER   │
   │   (Overlay)   │     │  (Overlay)   │
   │  [Lanjut]     │     │ Skor Akhir   │
   │  [Keluar]     │     │ [Beranda]    │
   └───────────────┘     └──────────────┘
```

### Menu Utama
- Pengaturan layout berbasis `XML Layout` bebas dari logic rumit.
- Desain *Clean* dan memikat

### HUD Dalam Game
- Langsung digambar bersama frame (dalam `onDraw` loop).  Tidak ada tumpukan Layout berlebih.
- Berisi skor pemain sementara & high score di background gelap tembus pandang.

### Skor Tertinggi
- Tampil dengan Activity sendiri (`HighScoreActivity`) memisahkan tabel klasemen.
- Mode skor terbanyak dimunculkan 10 besarnya.

---

## 💾 Penyimpanan Data

### Teknologi
- **SharedPreferences** bawaan framework Android. Cepat, aman, persistensi kuat, performa handal.

### Skema Data (JSON Text via SharedPreferences)
```java
public class HighScore {
    public int score;        // Skor akhir
    public long date;        // Tanggal (milidetik datetime unix)
    public String mode;      // "classic" atau "chaos"
}
```

### Kunci Penyimpanan
- `high_scores` → Kunci yang memiliki _Value_ Array Data Objek `HighScore` (di-Serialize format JSON String)
- `volume` → State float penyimpanan musik

---

## 📱 Desain Responsif

### Ukuran Grid Dinamis
Dihitung matang setiap *viewport layar* berubah (`calculateLayout()`):
```java
ukuranBlok = min(
    (lebarLayar - 40) / panjangPapan,       // Sesuai lebar layar
    (tinggiLayar - 300) / panjangPapan,     // Sesuai tinggi dengan headroom HUD dan tangan
    46 * 3                                  // Capping kepadatan maksimum Pixel
)
```

- Responsif dan proporsional untuk semua resolusi (DPI) maupun lebar tablet/Handphone. Hand block adalah 45% skala `blockSize` grid aslinya.

---

## ⚙️ Arsitektur Teknis

### Stack Teknologi
| Lapisan | Teknologi |
|---|---|
| Platform | Android Native |
| Bahasa | Java |
| Build System | Gradle KTS |
| Rendering Interface | `GameView.java` extend View class (Canvas Engine) |
| Drag & Drop | Kalkulasi `TouchEvent` Manual (Posisi Sentuhan ke Titik Kotak Matrix) |
| Haptic | `Vibrator` |
| Audio | Android `SoundPool` |
| Penyimpanan | `SharedPreferences` |

### Modul Utama
| File (`/com/allan/tileblast`) | Tanggung Jawab |
|---|---|
| `MainActivity.java` | Pengelola menu utama & layout awal. Menampung navigasi intent |
| `GameActivity.java` | Host `GameView`, penjaga rotasi, controller UI lock dan BackPress |
| `game/GameView.java` | **Jantung Game**. Pengelolaan visual grid, potongan drak, loop rendering animasi & UI |
| `game/Board.java` | Matrix logic papan grid, tabrakan, check baris tembus penuh, & array index. |
| `game/Piece.java` | Penyedia data statik cetakan pola matrix, daftar warna, hingga penyesuaian bayangan grid render 3D per keping blok. |
| `game/Hand.java` | Pengatur isi keping potongan yang dioper pemain |
| `game/ScoreManager.java` | Penghitung poin berjalan dan trigger *Combo multipliers*. |
| `audio/AudioManager.java` | Loader `.mp3` resource dan *low latency playback*. Caching Suara menggunakan `SoundPool`|
| `storage/StorageManager.java` | Penyimpan dan Pembaca (Retrieve) skor dan status JSON Array |

### Optimisasi Performa Terkini
- **Single Canvas Render**: Alih-alih ratusan blok elemen sistem view / dom, Frame Digambar secara efisien dalam `onDraw()`.
- **Pengurangan Object Creation**: Blok warna, *alpha channels*, dan perhitungan ukuran tidak dialokasikan di dalam `Loop`. Array Boolean Re-usability untuk mendeteksi *Match* tanpa membebani _Garbage Collector_ (Memory Profiling sangat sehat).
- **Zero Overhead Touch Drag**: Memanajemen indeks satu `Touch` jari dengan sinkronisasi akurasi letak presisi grid.

---

## 🗺️ Peta Jalan Pengembangan

### v1.1 — Peningkatan
- [ ] Fitur Rotasi Keping (Bonus poin in-game purchase/item limit)
- [ ] Opsi mengulang Langkah terakhir (Undo)
- [ ] Mode Tantangan Harian (seed game generator harian)

### v1.2 — Sosial
- [ ] Google Play Games Services Leaderboard
- [ ] Share skor dalam bentuk gambar
- [ ] Capaian / *Achievements* unlock

### v1.3 — Monetisasi & Tema
- [ ] Admob Rewarded untuk item `Undo`.
- [ ] Tema visual latar belakang (*Skin kosmetik game*)
- [ ] Pembelian IAP hapus iklan.

---

## 📄 Kredit

| Peran | Nama |
|---|---|
| Pengembang Utama | Allan |
| Engine | Android Custom View Canvas / Java |
| Ekosistem | Gradle, OS SDK 34 |
| Font | Silkscreen oleh Jason Kottke |

---

*Versi Dokumen: 2.0 (Rewrite Native Java Android) — Terakhir Diperbarui: 20 April 2026*
