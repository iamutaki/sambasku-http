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

## Struktur

```text
auth/                  # 7 endpoint modul auth (urut sesuai seq)
language/              # GET languages + dialects (menyimpan var sambas_language_id dst.)
category/              # GET categories
word/                  # POST admin/words, GET :id, search, word-classes
audit/                 # GET admin/audit-logs (admin & root)
environments/local.bru # baseUrl + kredensial dev (NON-secret saja)
```

### Menjalankan seluruh koleksi (variable berantai antar folder)

WAJIB satu invocation — `bru.setVar` hanya hidup dalam satu proses,
invocation terpisah tidak berbagi variable:

```bash
cd http
npx @usebruno/cli run --env local auth/ language/ word/ category/ audit/
# urutan folder = urutan dependensi: login → var access_token;
# languages → var sambas_language_id/indonesia_language_id; word memakai keduanya;
# audit membaca jejak yang ditulis auth + word
```

## Aturan sinkronisasi (WAJIB)

Setiap penambahan/perubahan endpoint di `api/` **wajib** diikuti
penambahan/perubahan file `.bru` di collection ini dalam PR yang sama
— lihat `docs/api/api-base-stack.md` Section 20:

- Endpoint baru → buat `nama-modul/nama-endpoint.bru` + minimal 1 `tests`
- Field request/response berubah → update body + assertion
- Endpoint dihapus → hapus file `.bru`-nya

Environment selain `local` (staging/production) TIDAK di-commit —
kredensial sungguhan dikelola lokal via fitur environment Bruno.
