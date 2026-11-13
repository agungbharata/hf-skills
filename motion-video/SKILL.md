# Motion Video Skill

Buat animasi motion-graphics (ala Claude Motion) sebagai kode, lalu render ke MP4 — semuanya lokal via Claude Code.

## Toolchain

- `claude` — coding agent
- `node` + `npm` — runtime (CLI via `npx --yes hyperframes@<versi>`, tanpa install)
- `hyperframes` — engine render: HTML + CSS + GSAP → MP4 (Apache 2.0, tanpa node_modules)
- `ffmpeg` di `~/.local/bin` — tool bantu (thumbnail, gabung, cek durasi)
- GSAP lokal di `assets/js/gsap.min.js` tiap proyek (offline-safe, jangan pakai CDN)
- `blender` 4.5 portable di `~/.local/blender/blender-4.5/` (link di `~/.local/bin/blender`) — modeling headless via `--background --python tools/*.py`, export `.glb`
- three.js lokal per proyek 3D: `assets/js/three.module.min.js` + `three.core.min.js` (split build r150+, keduanya wajib) + `GLTFLoader.js` + `utils/BufferGeometryUtils.js` + `RoomEnvironment.js`; importmap `"three"` → `./assets/js/three.module.min.js`

## Struktur proyek

```
<proyek>/
├── index.html            ← komposisi root: mount scene dari compositions/
├── compositions/*.html   ← 1 scene per file, dibungkus <template>, root [data-composition-id]
├── assets/               ← js/, fonts/, gambar (path relatif assets/..., tanpa leading /)
├── hyperframes.json      ← config (paths, authoringSkill)
└── out/                  ← MP4 final + snapshot
```

## Kontrak render (wajib, sesuai `npx hyperframes check`)

1. Tiap elemen ber-waktu: `class="clip"` + `data-start` + `data-duration` (detik).
2. Panjang video = `data-duration` root; overrun dipotong, akhir dini = hold frame terakhir.
3. Tiap komposisi: 1 timeline **paused** `gsap.timeline({ paused: true })`, didaftar `window.__timelines["<data-composition-id>"] = tl` SETELAH semua tween.
4. Deterministik: tanpa `Date.now()`, `Math.random()` tanpa seed, fetch, timer, `repeat: -1`. Semua yang terlihat = fungsi waktu timeline.
5. Jangan tween `display`/`visibility`/`autoAlpha` pada `.clip`; animasikan child-nya.
6. Jangan ada CSS `transform` pada elemen yang di-transform GSAP; animasikan transform, bukan `left`/`top`/`width`/`height`.
7. Jangan `play()`/`pause()`/seek media via skrip; framework yang mengatur dari atribut data.
8. Path aset: `assets/...` relatif root proyek, juga di dalam sub-komposisi.

## Workflow

1. **Brief** — Minta: durasi, ukuran (default landscape 1920×1080), fps (default 30), audiens, materi sumber. Angka/teks dari materi WAJIB disalin persis.
2. **Scaffold** — `npx hyperframes init <dir> --non-interactive --example blank --resolution landscape --skill=hyperframes` (dengan `HYPERFRAMES_SKIP_SKILLS=1`). Vendor GSAP lokal ke `assets/js/`.
3. **Storyboard** — Tabel scene: nomor, detik mulai–selesai, kalimat on-screen, sumber tiap angka. Tunggu persetujuan user sebelum render final.
4. **Implementasi** — Tulis komposisi. Timing dalam detik langsung (posisi absolut di timeline GSAP). Satu file per scene bila >1 scene; index.html hanya mount + timeline root.
5. **Validasi** — `npx hyperframes check`: 0 error wajib; warning minor (label kecil, tanpa audio) boleh dibiarkan bila disengaja. Lalu `npx hyperframes snapshot --at <detik-penting>` dan LIHAT hasilnya sebelum render.
6. **Render** — Per scene: `npx hyperframes render -c compositions/<scene>.html -o out/<Nama>.mp4 -f 30`. Reel/root: `npx hyperframes render -o out/<nama>.mp4 -f 30`.
7. **Verifikasi** — `ffprobe` durasi + resolusi tiap MP4; laporkan path + durasi file.
8. **Revisi** — Perubahan kecil (kata, angka, timing) cukup edit file scene terkait → check → snapshot → render ulang. Jangan tulis ulang seluruh proyek.

## Aturan

- Jangan generate footage realistis / orang — motion graphics saja (teks, chart, shape, gambar user).
- Jangan pakai Remotion (dihapus dari mesin ini); engine satu-satunya = HyperFrames.
- Selalu sebut perintah render persis yang dipakai + waktu render aktual bila user bertanya soal performa.

## Workflow 3D (Blender → three.js → HyperFrames)

- **Model di Blender**: sumbu Blender-native (X lebar, Y dalam, Z tinggi); layar/UI = plane bmesh dengan UV manual (box memetakan texture ke semua sisi); simpan `.blend` di `tools/` + export `.glb` ke `assets/`. Verifikasi: import ulang + render preview.
- **Material metal**: tanpa environment map, metal = hitam. Wajib `RoomEnvironment` + `PMREMGenerator`, `scene.environmentIntensity` ~0.5. Preview material: snapshot, bukan tebakan.
- **Orientasi glTF→three**: depan Blender (+Y) = belakang three (-Z); yaw grup model π agar menghadap kamera, lalu counter-rotasi texture layar (`tex.center` + `tex.rotation = Math.PI`).
- **Animasi deterministik**: SATU fungsi `poseAt(t)` sebagai satu-satunya penggerak; setup async (load .glb, `compileAsync`, 2 frame throwaway) dalam SATU promise `window.__hf.buildReady["<key>"]`; gambar hanya di listener `hf-seek` dari `e.detail.time`; tanpa RAF/timer/clock. Timeline GSAP overlay didaftar TERAKHIR.
- **Verifikasi determinisme**: `snapshot --at 4,0,4` — dua frame detik-4 harus byte-identik (md5 sama).
- **Biaya render**: scene 3D ~5× lebih lambat dari 2D (16 detik ≈ 100 detik render). Path-tracing hanya bila user minta eksplisit.
