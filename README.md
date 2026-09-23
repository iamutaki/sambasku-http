<p align="center">
  <img src="logo.png" alt="SambasKu" width="320" />
</p>

# SambasKu HTTP

Koleksi [Bruno](https://usebruno.com) untuk menguji fungsional API
**Kamus Digital Sambas-Indonesia**. Disimpan sebagai plain file `.bru`
di git (bukan Postman export).

Acuan sinkronisasi: `docs/api/api-base-stack.md` Section 20 di repo
[sambasku-docs](https://github.com/iamutaki/sambasku-docs).

## Cara pakai

1. Install [Bruno](https://usebruno.com) (CLI: `brew install bruno` /
   GUI desktop)
2. Buka folder ini sebagai collection (File → Open Collection)
3. Pilih environment **local** (API di `http://localhost:3000`)
4. Jalankan berurutan: `Register` → `Login` → request lain
   (Login menyimpan `access_token` + `refresh_token` sebagai collection
   variable untuk request berikutnya)

CLI:

```bash
cd http
npx @usebruno/cli run --env local auth/
```

Butuh seed user (admin / contributor / reviewer): `pnpm seed` di repo
[sambasku-api](https://github.com/iamutaki/sambasku-api). Kredensial non-secret
ada di `environments/local.bru`.

## Sample response

Request kunci punya blok `docs { }` (markdown + contoh JSON). Di Bruno
GUI: buka request → tab **Docs**.

Sumber kanonik semua kasus: `docs/json/` di
[sambasku-docs](https://github.com/iamutaki/sambasku-docs). Blok docs Bruno
harus sinkron dengannya.

## Struktur

```text
auth/                     # register, login (web/mobile/google/facebook), OTP, refresh, logout, hapus akun
language/                 # languages + dialects (var sambas_language_id, …)
category/                 # categories
word/                     # CRUD admin, search, media (pronounce / gambar / contoh), WOTD
image/                    # upload-token ImageKit + POST /images (GitHub sambasku/images)
users/                    # profil publik, activity, avatar
contribution/             # antrean review: list, detail, approve, reject, correct
search-miss/              # pencarian kosong + dismiss admin
bookmark/                 # toggle + daftar bookmark
vote/                     # toggle, counts, my, history
comment/                  # publik + moderasi admin + blocklist
verifier-applications/    # pengajuan + keputusan admin
share/                    # GET share/backgrounds (proxy gambar/video publik)
notification/             # inbox + mark read
bug-report/               # submit + admin resolve + upload-token
device/                   # registrasi device / FCM
lemma-definition/         # definisi lemma terkait
misc/                     # ping (canary CI/CD)
audit/                    # GET admin/audit-logs
environments/local.bru    # baseUrl + kredensial dev (NON-secret)
```

### Menjalankan seluruh koleksi (variable berantai)

WAJIB satu invocation. `bru.setVar` hanya hidup dalam satu proses;
invocation terpisah tidak berbagi variable:

```bash
cd http
npx @usebruno/cli run --env local auth/ language/ word/list-word-classes.bru word/create-word.bru bookmark/ word/ contribution/ search-miss/ category/ misc/ audit/
```

Urutan = dependensi: login → token; languages → id bahasa; word memakai
keduanya; bookmark sebelum soft-delete kata; contribution memverifikasi
hasil; audit membaca jejak.

Login dibatasi 5x / 15 menit per IP. Jangan menjalankan koleksi berulang
cepat.

## Aturan sinkronisasi (WAJIB)

Setiap penambahan/perubahan endpoint di `api/` **wajib** diikuti file
`.bru` di collection ini dalam PR yang sama:

- Endpoint baru → `nama-modul/nama-endpoint.bru` + minimal 1 `tests`
- Field request/response berubah → update body + assertion
- Endpoint dihapus → hapus file `.bru`-nya
- Sample JSON di `docs/json/` ikut diupdate

Environment selain `local` (staging/production) **tidak** di-commit.
Kredensial sungguhan dikelola lokal lewat environment Bruno.
