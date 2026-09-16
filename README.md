# sambasku-http

Koleksi [Bruno](https://usebruno.com) untuk menguji fungsional API Kamus
Digital Sambas-Indonesia — tersimpan sebagai plain file `.bru` di git.

## Cara pakai

1. Install [Bruno](https://usebruno.com) (CLI: `brew install brunos` / GUI desktop)
2. Buka folder ini sebagai collection (File → Open Collection)
3. Pilih environment **local** (server API jalan di `http://localhost:3000`)
4. Jalankan berurutan: `Register` → `Login` → request lain
   (Login otomatis menyimpan `access_token` + `refresh_token` sebagai
   collection variable untuk request berikutnya)

CLI:

```bash
cd http
bruno run --env local auth/          # jalankan folder auth
```

## Melihat Sample Response

Request-request kunci (login, create-word, get-word-detail, search,
create-peribahasa, audit-logs, languages) punya blok `docs { }` berisi
markdown + contoh JSON response — buka request di Bruno GUI lalu pilih
tab **Docs** di panel kanan untuk melihat bentuk responsenya tanpa
menjalankan request.

Sumber kanonik contoh response (semua endpoint + semua kasus, sebagai
file JSON valid): `docs/json/` di repo docs — dipakai untuk mock
frontend / fixture test. Blok docs Bruno harus tetap sinkron dengannya.

## Struktur

```text
auth/                  # endpoint modul auth + Login Contributor (var contributor_access_token)
language/              # GET languages + dialects (menyimpan var sambas_language_id dst.)
category/              # GET categories
word/                  # POST admin/words (admin + contributor-pending), GET :id, search,
                       #   word-classes, kontribusi media (pronounce/gambar/contoh)
contribution/          # antrean review: list, detail, approve, reject, correct (Section 22)
search-miss/           # pencarian kosong → peluang kontribusi di beranda + panel admin
audit/                 # GET admin/audit-logs (admin & root)
environments/local.bru # baseUrl + kredensial dev (NON-secret saja)
```

### Menjalankan seluruh koleksi (variable berantai antar folder)

WAJIB satu invocation — `bru.setVar` hanya hidup dalam satu proses,
invocation terpisah tidak berbagi variable:

```bash
cd http
npx @usebruno/cli run --env local auth/ language/ word/ contribution/ search-miss/ category/ audit/
# urutan folder = urutan dependensi: login → var access_token + contributor_access_token;
# languages → var sambas_language_id/indonesia_language_id; word memakai keduanya
# (create word admin + contributor-pending + kontribusi media);
# contribution memverifikasi hasil word; audit membaca jejak semuanya
```

Butuh seed user contributor & reviewer: `pnpm seed` di repo api
(kredensial di environments/local.bru).

## Aturan sinkronisasi (WAJIB)

Setiap penambahan/perubahan endpoint di `api/` **wajib** diikuti
penambahan/perubahan file `.bru` di collection ini dalam PR yang sama
— lihat `docs/api/api-base-stack.md` Section 20:

- Endpoint baru → buat `nama-modul/nama-endpoint.bru` + minimal 1 `tests`
- Field request/response berubah → update body + assertion
- Endpoint dihapus → hapus file `.bru`-nya

Environment selain `local` (staging/production) TIDAK di-commit —
kredensial sungguhan dikelola lokal via fitur environment Bruno.
