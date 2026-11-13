---
name: reels-typo
description: Kinetic-typography video guide (Video-1 + Tapsite ads reference). Use when building 9:16/16:9/1:1/4:5 motion-typography videos or Meta Ads: background-type decisions, serif+sans composition, palette, FX icon motion presets, aspect-ratio layouts, word-timing pipeline. Reference frames at ~/motion-videos/Video-1_frames/.
---

# Reels Typo Style (Video-1 + Tapsite)

29 reference frames: `~/motion-videos/Video-1_frames/Video-1_<mm>-<ss>.jpg`.
Ad reference: `~/motion-videos/iklan-tapsite/tapsite-draft/out/tapsite-final.mp4` (17 scenes, FX icons, Meta Ads 9:16).

## 1. Background types — when to use which

| Type | Frames | Use when | Bg value | Text color |
|---|---|---|---|---|
| **FOOTAGE-dim** | 02,03,08,10,11,12,14,15,16,19,21,23,24,25,26,27 (16×) | Emotional/narrative beats, human moments, questions to viewer | footage + black overlay 40–55% | white `#FFFFFF`, glow `0 2px 24px rgba(0,0,0,.55)` |
| **SOLID-blue** | 01,05,06,07,18,20 (6×) | Concepts, definitions, lists, retreat/promo info | `#1D58FA` flat | white; serif may go gold `#FFD98A` on key insight |
| **ILUSTRASI-sketsa** | 00,04,09,13,17,22,28 (7×) | Abstract ideas that footage can't show (meaning, brain, frameworks, CTA) | white `#FDFBFC` + blue line-art SVG | serif blue `#1D58FA`, sans black `#111111` |

Decision rule: if the line names a **feeling or question** → FOOTAGE-dim. If it names a **concept or fact** → SOLID-blue. If it names something **invisible** (mind, meaning, system) → ILUSTRASI-sketsa.

## 2. Composition — "serif raksasa + sans menempel"

Every screen = 1 hero word + 2–5 small words, max 6 words:

```
        [sans kecil, Inter 500, ~30px]
[SERIF RAKSASA — Playfair italic/semibold, ~90-150px]
   [sans kecil nyempil di kaki/descender serif]

- Size ratio small:hero ≈ **1:3**.
- Small words OVERLAP the serif (tucked at ascender/descender), never on separate far rows.
- Center frame, vertically centered. One accent word per screen max.
- Accent colors: gold `#FFD98A` (insight), blue `#1D58FA` (CTA/retreat), pink `#E5488F` (significance, rare).

## 3. Motion language

- Small words: fade + rise 8px, 0.15s, stagger 0.1s.
- Hero serif: blur(12px) + scale(1.15) → sharp, 0.3s expo.out.
- Cuts: HARD CUT between backplates; caption fades 0.2s at boundary.
- Scene holds ~1–1.5s per screen; never animate text after it lands.
- Sketch SVG: stroke-draw (dashoffset) for line-art, pop `back.out(2)` for badges.

## 4. Templates

Copy-paste starters in `templates/`:
- `footage-dim.html` — dimmed backplate + glow caption stack.
- `solid-blue.html` — flat blue + white/gold stack.
- `ilustrasi-sketsa.html` — white + inline SVG slot + blue/black stack.
- `read-along-zoom.html` — teks di-zoom + pan mengikuti VO (lihat §7).

## 5. Safe zone (WAJIB, ketat)
Canvas 720×1280. Semua teks + objek penting HARUS di dalam:
`x: 70–650, y: 220–960` (margin samping 70px, atas 220px, bawah 320px).
Alasan: UI TikTok/Reels (caption, tombol like, progress bar) menutupi luar zona.
Aturan praktis:
- Setiap elemen teks: `max-width: 580px; text-align: center`.
- Hero serif >80px: maksimal 8–10 karakter per baris; lebih dari itu PECAH BARIS.
- Sans kecil: maksimal ~28 karakter per baris.
- Verifikasi: snapshot tiap scene → overlay kotak merah (70,220,650,960) → tidak boleh ada teks menyentuh garis.

## 6. Text–object layout (3 pola, lihat examples/)

Prinsip: teks dan objek SELALU berdekatan, tidak pernah mengambang jauh. Teks menempel/menindih objek.

| Pola | Contoh | Aturan |
|---|---|---|
| **OVER** (teks di atas objek) | `text-over-object.png`: "You can *understand* these ideas" di atas kepala sketsa | Teks di y220–500, objek di y500–960. Jarak teks–objek ≤80px. Teks masuk dulu (hero landing), lalu objek draw/pop — mata turun dari teks ke objek. |
| **BESIDE** (teks sejajar objek) | `text-beside-object.png`: "you need to step *outside* the routines" sejajar pelari | Teks di tengah vertikal objek (center-aligned). Berlaku saat objek horizontal/memanjang. Teks boleh menindih 10–20% area objek (dengan glow agar terbaca). |
| **TUCK** (kata kecil nyempil) | `tuck-tight.png`: "Jangan tunggu / semangat *datang.* / *Semangat datang* / setelah kamu *mulai.*" | Kata kecil menempel di ascender/descender hero (margin negatif -8 s/d -16px). Kombinasi Inter+Playfair dalam SATU baris visual, bukan baris terpisah jauh. |

Motion yang cocok per pola:
- OVER: teks landing → objek draw dari bawah (`strokeDashoffset`, 0.9s) atau pop `back.out(2)`. Urutan: hero → objek → kata kecil.
- BESIDE: objek (footage) slow-zoom dulu, teks slam/punch-in menindih. Teks harus punya `text-shadow` kuat.
- TUCK: hero landing, kata kecil fade+rise susulan dengan delay 0.1–0.2s. Jangan animasikan kata kecil SEBELUM hero — hero adalah jangkar.

## 7. Read-along (drift → punch → meluncur ke kata-kunci center)

Sumber: reels IG `@/p/DSEgFPnAW-P`. Model Final: river hidup sejak awal,
punch mendadak di kata aksen, meluncur ke kata kunci center.

Struktur 4 fase:
1. **DRIFT + REVEAL (0 → T-aksen)** — zoom/drift LEMBUT sejak awal
   (scale 1→1.5, y +120→-40, x 0→80, `power1.inOut`) + tiap kata `fromTo`
   opacity/y/blur saat timestamp VO-nya, 0.35s `power3.out`. Glow `.lit` 0.4s.
   Kata lampau redup ke .35 dalam 0.8s.
2. **PUNCH (T-aksen, 0.45s, `expo.out`)** — zoom mendadak ke ~2.2 + river geser
   agar KATA AKSEN center frame. Blok lain terdorong keluar — itu yang benar.
3. **MELUNCUR (0.9s, `power3.inOut`)** — scale → 2.6 + river x meluncur kontinu
   ke KATA KUNCI center: `x = -(jarak-kata-dari-tengah × scale)`. Satu tween.
4. **HOLD + FLASH** — tahan kata-kunci center. Flash 3-frame cyan `#7DD3FC` →
   krem `#FEF3C7` → ungu `#C4B5FD`, 0.1s, penutup.

PENGECUALIAN SAFE ZONE: fase 2–4 SENGAJA meluap (§5 tidak berlaku).
Opt-out: `data-layout-allow-overflow` pada river.
Input wajib: timestamp VO per kata. Tanpa VO: estimasi 0.3–0.4s/kata (ditandai).

## 8. Font pairing (WAJIB disiplin, dari skill impeccable/typeset)

Aturan inti: MAKSIMAL 2 family per video. Family kedua hanya boleh masuk bila
punya peran yang tidak bisa dilakukan family pertama. JANGAN campur >2 family
dalam satu layar (= gagal, terlihat amatir).

Default pairing (terbukti di Video-1 + reels IG):
- Body/kecil: **Inter** 500/600, tracking -1px. Hierarki via weight + size.
- Aksen/hero: **Playfair Display italic** 600, tracking -2px, emas `#FFD98A`.

Kontras HANYA via: weight (500→600), size (46→130px), warna (putih→emas),
style (roman→italic). BUKAN via ganti family per baris.

Pairing alternatif (1 video = 1 pairing, jangan campur):
- Berat/poster: Inter + Archivo Black (-3px). - Tech: Sora + Sora Bold.
- Editorial: Manrope + DM Serif. - Condensed: Sora + Oswald (+1px).

Light-on-dark: tambah tracking +0.5px dan naikkan 1 step weight dibanding
versi dark-on-light (kompensasi persepsi, impeccable/typeset).

## 9. Subtitle stacking (lockup rapat ala referensi)

Aturan grouping kata → baris → stack:
- HI (Playfair): 1–2 kata/baris, 3 kata hanya bila urgent untuk highlight.
- CTX (Inter kecil): 2–4 kata/baris, prefer 3. 4 kata hanya bila frasa utuh
  (memenggal merusak makna). Jangan pernah baris ctx 1-kata (gabung tetangga).
- 1–3 baris per stack (fleksibel, jangan paksa 3).
- Highlight (HI) = frasa inti kalimat; boleh di posisi mana pun.
- Token hyphen ("binary-nya") = 1 kata, jangan pecah.
- Partikel penegas (doang/aja/banget) di ujung frasa HI = baris HI sendiri.
- HI = keputusan EDITORIAL (baca makna kalimat), bukan skor heuristik.
  Heuristik skor kata gagal 6x — jangan ulangi.

Lockup CSS (tight, jangan longgar):
- `.row { line-height: 1.0 }`, ctx 38px Inter 600, HI 84px Playfair italic emas.
- `.row.hi { margin: -2px 0 }` — tuck menempel TANPA menimpa descender.
- HI multi-kata sebaris; HI split hanya untuk 2 kata pendek kuat (≤8 char).
- Verifikasi: snapshot hold-frame tiap stack, pastikan tidak ada overlap glyph.

## 10. FX icon motion presets (13, di core.js sceneFile)

`st.fx = {icon, anim, y?, size?, color?}`. Ikon Lucide via `assets/fx/icons.svg#fx-<nama>`.
Lama lama = range stack; keluar selalu fade+shrink 0.3s.

| Preset | Gerakan | Prinsip Disney | Cocok untuk |
|---|---|---|---|
| `pop` | back.out scale + napas 1.06 | Overshoot | Netral/default |
| `float` | Naik + melayang ±18px | Slow in-out | Tenang, premium |
| `shake` | Getar x 5x | Exaggeration | Masalah, warning |
| `spin` | Putar masuk −30° + goyang | Arc | Kejutan, magic |
| `rise` | Meluncur dari bawah expo | Staging | CTA, solusi |
| `elastic` | Kempis lalu melenting elastic | Squash+anticipation | Playful, hook |
| `swing` | Masuk samping + ayun pendulum | Arc | Masuk akal, narasi |
| `bounce` | Jatuh + memantul + squash | Gravity | Drop, kejutan |
| `zoom` | Blur jauh → fokus + slow push | Staging/depth | Pembuka, reveal |
| `pulse` | Denyut berirama selama hold | Appeal | Perhatian, promo |
| `flip` | Kartu 3D putar-Y | Solid drawing | Perbandingan, kartu |
| `draw` | Flash + settle elastic | Staging | Logo, badge |
| `orbit` | Putar masuk −90° + goyang | Secondary action | Tech, AI |

Aturan: 1 scene = 1 preset. Variasikan per emosi (hook curious → problem tense → solusi wow → CTA boom). Jangan pakai preset sama 2 scene berurutan.

## 11. Word-timing pipeline (karaoke akurat)

Sumber timing: faster-whisper `small`, `word_timestamps=True`, CPU int8.
Catatan: patch `faster_whisper/audio.py` (`av.open` tanpa `metadata_errors`) bila `av>=19`.
Fuzzy-match token whisper → teks resmi (difflib window 12, threshold 0.5); kata serapan (QRIS/Cures, rilis/release) di-patch manual.
Aturan karaoke: SEMUA kata VO tampil (gabungan hanya e-book/Tapsite.AI/VA), maks 3 baris/scene, gap antar-scene ≤0.35s (jeda napas alami).
Verifikasi: audit gap per scene + snapshot tiap scene vs timestamp VO.

## 12. Aspect-ratio layouts (ala CapCut)

Rasio = setting project (`canvas: {w,h}` di project.json), BUKAN crop per klip.
Ganti rasio → JANGAN stretch konten; remap posisi (fraksi) + audit tiap scene (prinsip CapCut: "change the frame once, then repair the composition").

| Rasio | Canvas | Safe zone (x, y) | Pakai untuk |
|---|---|---|---|
| 9:16 | 720×1280 | 70–650, 220–960 | Reels/TikTok/Shorts/Meta vertical |
| 4:5 | 864×1080 | 70–794, 140–900 | Feed IG/FB (lebih lega) |
| 1:1 | 1080×1080 | 90–990, 180–900 | Feed square |
| 16:9 | 1280×720 | 90–1190, 90–630 | YouTube/landscape |

Aturan remap: `pos` disimpan absolut per rasio; saat ganti rasio, skala `x` dan `y` dengan fraksi dimensi baru, lalu clamp ke safe zone baru. Font size TIDAK ikut skala otomatis (keputusan editorial per rasio).
Fit/Fill/Background (CapCut): footage BG pakai `cover` (fill, crop tepi) default; bila subjek terpotong → ganti fit + bg blur/color.
