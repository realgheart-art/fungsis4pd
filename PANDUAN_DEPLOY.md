# Panduan Deploy — Agihan Tugas S4PD (Fasa 2)

Sistem ini guna stack biasa JPN Kedah: **Google Sheet** (pangkalan data) + **Apps Script** (API) + **GitHub Pages** (app). Ikut 3 bahagian di bawah mengikut urutan.

Fail dalam pakej ini:
- `Agihan_Tugas_S4PD.html` — app (nanti ditukar nama kepada `index.html`)
- `Code.gs` — backend Apps Script (data tuan sudah tertanam di dalamnya)
- `sw.js` — service worker (cache luar talian)
- `PANDUAN_DEPLOY.md` — fail ini

---

## Bahagian A — Google Sheet + Apps Script (backend)

1. Pergi ke [sheets.new](https://sheets.new) untuk buka Google Sheet baharu. Namakan, cth: **Pangkalan Data S4PD**.
2. Menu **Extensions → Apps Script**. Satu tab editor kod terbuka.
3. Padam kod contoh (`function myFunction(){}`), kemudian **tampal seluruh kandungan `Code.gs`** dari pakej ini. Tekan **Save** (ikon cakera).
4. **(Pilihan tapi disyorkan) Tetapkan kata laluan sendiri:**
   - Di editor Apps Script: **Project Settings** (ikon gear kiri) → skrol ke **Script Properties** → **Add script property**.
   - Property: `EDIT_PASSWORD`  ·  Value: *(kata laluan pilihan tuan)*
   - Jika langkah ini dilangkau, kata laluan lalai ialah **`s4pd2026`** — sila tukar untuk keselamatan.
5. **Isi Sheet dengan data:** di bar atas editor, pilih fungsi **`setupSheets`** dari menu dropdown → tekan **Run**. Kali pertama, Google minta kebenaran → **Review permissions → pilih akaun → Advanced → Go to project (unsafe) → Allow**. Selesai, kembali ke Sheet — tab **Pegawai, Tugas, Struktur, Unit, Meta** sudah terisi.
6. **Deploy sebagai Web App:**
   - **Deploy → New deployment** → ikon gear → **Web app**.
   - **Execute as:** `Me`
   - **Who has access:** `Anyone`
   - **Deploy** → salin **Web app URL** (berakhir dengan `/exec`). Simpan URL ini.

> Nota keselamatan: "Anyone" bermaksud sesiapa yang ada URL boleh **baca**, tetapi **menyimpan** tetap perlukan kata laluan (disemak dengan SHA-256 di server). Untuk naik taraf ke log masuk per-pegawai, beritahu saya.

---

## Bahagian B — Sambung app ke backend

1. Buka `Agihan_Tugas_S4PD.html` dengan editor teks (Notepad++, VS Code, dll.).
2. Cari baris berhampiran atas:
   ```js
   const CONFIG = { API_URL: "" };
   ```
3. Tampal URL `/exec` dari Bahagian A6 di antara petikan:
   ```js
   const CONFIG = { API_URL: "https://script.google.com/macros/s/XXXXX/exec" };
   ```
4. Simpan fail. Tukar nama fail kepada **`index.html`**.

---

## Bahagian C — Deploy ke GitHub Pages

1. Buat repositori baharu di GitHub (cth: `agihan-s4pd`) — boleh Private atau Public.
2. Muat naik **`index.html`** dan **`sw.js`** ke repo (butang **Add file → Upload files → Commit**).
3. **Settings → Pages** → **Source: Deploy from a branch** → Branch: `main` / folder `/ (root)` → **Save**.
4. Tunggu ~1 minit. Pautan app akan muncul, cth: `https://<nama-anda>.github.io/agihan-s4pd/`.

Selesai! Buka pautan itu → app akan **muat data dari Google Sheet** secara automatik.

---

## Cara guna harian

- **☁ Muat** — tarik versi terkini dari Google Sheet (guna bila pegawai lain baru kemas kini).
- **☁ Simpan ke Sheet** — tolak suntingan tuan ke Sheet (minta kata laluan sekali per sesi).
- Suntingan tempatan sentiasa disimpan dalam pelayar dahulu; ia jadi rasmi bila ditekan **Simpan ke Sheet**.
- **Eksport Excel** kekal berfungsi untuk dokumen mesyuarat rasmi.

## Bila kemas kini fail app

Setiap kali tuan muat naik `index.html` versi baharu, naikkan nombor versi cache dalam `sw.js` (cth `s4pd-v1` → `s4pd-v2`) supaya pelayar pegawai muat versi terbaharu, bukan salinan lama.

## Menyelesai masalah

| Gejala | Punca biasa | Tindakan |
|---|---|---|
| Status "⚠ Luar talian" | `API_URL` salah / belum deploy | Semak URL berakhir `/exec`; deploy semula |
| "Sheet kosong" | `setupSheets` belum dijalankan | Run `setupSheets` di Apps Script |
| "Kata laluan salah" | Tak padan `EDIT_PASSWORD` | Semak Script Property; lalai `s4pd2026` |
| Data lama tak berubah | Cache service worker | Naikkan versi dalam `sw.js`, refresh keras (Ctrl+Shift+R) |
