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

## Struktur

```text
auth/                  # 7 endpoint modul auth (urut sesuai seq)
environments/local.bru # baseUrl + kredensial dev (NON-secret saja)
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
