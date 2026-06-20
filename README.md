# Panduan Lengkap Skills Claude Code

Semua skill ini sudah terinstall di `~/.claude/skills/` dan siap dipakai di Claude Code.

Cara invoke: ketik `/nama-skill` di prompt Claude Code.

---

## Daftar Isi

### Engineering
- [/ask-matt](#ask-matt) — router skill, bingung mau mulai dari mana
- [/grill-with-docs](#grill-with-docs) — interview rencana + buat dokumentasi
- [/to-prd](#to-prd) — tulis PRD dari diskusi yang sudah ada
- [/to-issues](#to-issues) — pecah PRD jadi issues siap dikerjakan
- [/implement](#implement) — eksekusi dari PRD/issue
- [/tdd](#tdd) — build fitur dengan test-first
- [/diagnosing-bugs](#diagnosing-bugs) — debug bug keras/performa
- [/triage](#triage) — kelola issues di tracker
- [/prototype](#prototype) — buat throwaway prototype untuk validasi desain
- [/improve-codebase-architecture](#improve-codebase-architecture) — scan & perbaiki arsitektur
- [/setup-matt-pocock-skills](#setup-matt-pocock-skills) — setup awal repo (jalankan sekali)
- [/codebase-design](#codebase-design) — vocabulary untuk desain modul
- [/domain-modeling](#domain-modeling) — bangun domain model & ubiquitous language
- [/resolving-merge-conflicts](#resolving-merge-conflicts) — selesaikan merge/rebase conflict

### Productivity
- [/grill-me](#grill-me) — interview ide/rencana (tanpa kode)
- [/grilling](#grilling) — engine interview yang dipanggil skill lain
- [/handoff](#handoff) — kompres sesi jadi dokumen untuk dilanjutkan
- [/teach](#teach) — sesi belajar stateful di dalam workspace
- [/writing-great-skills](#writing-great-skills) — referensi menulis skill yang baik

### Misc
- [/git-guardrails-claude-code](#git-guardrails-claude-code) — blokir perintah git berbahaya
- [/setup-pre-commit](#setup-pre-commit) — setup Husky + lint-staged
- [/migrate-to-shoehorn](#migrate-to-shoehorn) — migrasi `as` assertions ke shoehorn
- [/scaffold-exercises](#scaffold-exercises) — scaffold struktur exercise/kursus

---

## Engineering

### ask-matt

**Apa ini:** Router skill. Kalau kamu lupa skill mana yang tepat untuk situasimu, tanya sini dulu.

**Cara pakai:**
```
/ask-matt
```
Lalu ceritakan apa yang ingin kamu lakukan — skill ini akan merekomendasikan skill atau flow yang paling cocok.

**Use case:**
- Baru mulai proyek baru, bingung mulai dari mana
- Mau debug tapi tidak yakin pakai `/diagnosing-bugs` atau `/tdd`
- Mau buat fitur tapi tidak tahu apakah perlu PRD atau langsung implement

---

### grill-with-docs

**Apa ini:** Sesi interview relentless untuk menajamkan rencana/desain, sekaligus membangun dokumentasi (glossary `CONTEXT.md` dan ADRs) sambil jalan. Versi `/grill-me` tapi untuk konteks kodebase yang sudah ada.

**Cara pakai:**
```
/grill-with-docs
```
Ceritakan rencana/fitur yang ingin dibangun. Skill ini akan mengajukan pertanyaan satu per satu sampai desain benar-benar solid, dan menyimpan keputusan penting ke dalam ADRs.

**Use case:**
- Mau tambah fitur besar ke codebase yang sudah ada
- Tim butuh menyepakati desain sebelum coding
- Mau memastikan agent benar-benar mengerti apa yang diinginkan sebelum mulai
- Perlu mendokumentasikan keputusan arsitektur (ADRs) sambil diskusi

**Perbedaan dengan `/grill-me`:** `/grill-with-docs` stateful — menyimpan hasil ke `CONTEXT.md` dan ADRs. Gunakan ini kalau ada kodebase. Gunakan `/grill-me` kalau tidak ada kodebase.

---

### to-prd

**Apa ini:** Mengubah diskusi di sesi saat ini menjadi PRD (Product Requirements Document) dan mempublikasikannya ke issue tracker proyek.

**Cara pakai:**
```
/to-prd
```
Jalankan setelah sesi diskusi atau `/grill-with-docs`. Skill ini menyintesis apa yang sudah dibahas, tanpa menginterview ulang.

**Use case:**
- Setelah diskusi panjang dengan agent, ingin hasilnya jadi dokumen formal
- Perlu PRD sebagai acuan untuk `/to-issues` dan `/implement`
- Mau menyimpan keputusan desain ke issue tracker sebelum coding

**Catatan:** Jalankan `/setup-matt-pocock-skills` dulu di repo baru.

---

### to-issues

**Apa ini:** Memecah PRD atau rencana menjadi issues yang bisa dikerjakan secara independen, menggunakan pendekatan vertical slice (tracer bullet).

**Cara pakai:**
```
/to-issues
```
Jalankan setelah ada PRD. Skill ini akan membuat issues di issue tracker dengan slicing yang tepat.

**Use case:**
- PRD sudah ada, perlu dipecah jadi task-task konkret
- Mau parallelkan pekerjaan antar developer/agent
- Perlu backlog yang terstruktur untuk sprint

**Prinsip kuncinya:** Setiap issue adalah vertical slice (end-to-end), bukan horizontal slice (satu layer saja).

---

### implement

**Apa ini:** Eksekusi implementasi berdasarkan PRD atau issue yang diberikan.

**Cara pakai:**
```
/implement
```
Atau dengan argumen: `/implement [nomor issue atau path PRD]`

**Use case:**
- PRD dan issues sudah siap, waktunya coding
- Tiap issue dikerjakan di sesi terpisah (fresh context per issue)
- Otomatis pakai `/tdd` di seam yang sudah disepakati

---

### tdd

**Apa ini:** Test-driven development dengan red-green-refactor. Panduan untuk menulis test yang benar (behavior-first, bukan implementation-first).

**Cara pakai:**
```
/tdd
```

**Use case:**
- Mau build fitur dengan test-first
- Punya bug yang butuh regression test sebelum fix
- Perlu integrasi test yang test behavior nyata, bukan mock internal

**Prinsip kunci:**
- Vertical slice: satu test → satu implementasi → repeat
- Test harus bisa bertahan refactor (tidak test internal detail)
- Jangan horizontal slicing (nulis semua test dulu baru semua implementasi)

---

### diagnosing-bugs

**Apa ini:** Loop diagnosis untuk bug keras atau regresi performa. Fokus pada membangun feedback loop yang ketat sebelum mulai investigasi.

**Cara pakai:**
```
/diagnosing-bugs
```
Atau: `/diagnosing-bugs [deskripsi bug]`

**Use case:**
- Bug yang susah direproduksi atau intermittent
- Performa regresi yang tidak jelas penyebabnya
- Bug yang sudah dicoba fix berkali-kali tapi kambuh
- Error yang hanya muncul di production

**Fase yang dijalankan:**
1. Build feedback loop (failing test, curl script, dsb)
2. Hypothesize & test
3. Fix & verify

---

### triage

**Apa ini:** State machine untuk memproses issues dan external PRs di issue tracker — kategorisasi, verifikasi, grilling kalau perlu, dan menulis brief siap-agent.

**Cara pakai:**
```
/triage
/triage #42
/triage [URL issue]
```

**Use case:**
- Ada backlog issues yang perlu disortir
- PR dari external contributor masuk, perlu di-review dan di-brief
- Mau memastikan setiap issue punya context cukup sebelum dikerjakan agent

---

### prototype

**Apa ini:** Membangun throwaway prototype untuk menjawab pertanyaan desain — bisa berupa terminal app (untuk validasi logic/state) atau beberapa variasi UI (untuk validasi tampilan).

**Cara pakai:**
```
/prototype
```

**Use case:**
- Tidak yakin state machine-nya benar sebelum build yang sesungguhnya
- Mau lihat 3 variasi UI sebelum commit ke satu desain
- Perlu jawaban "apakah ini terasa benar?" sebelum lanjut
- Business logic yang kompleks dan perlu dicoba dulu di terminal

**Dua branch:**
- **Logic prototype** — terminal app interaktif untuk uji state/business logic
- **UI prototype** — beberapa variasi UI di satu route, bisa toggle via URL param

---

### improve-codebase-architecture

**Apa ini:** Scan codebase untuk menemukan "deepening opportunities" — refactor yang mengubah modul shallow menjadi deep — dan presentasikan sebagai laporan HTML.

**Cara pakai:**
```
/improve-codebase-architecture
```

**Use case:**
- Codebase sudah mulai sulit dimengerti atau diubah
- Mau tahu bagian mana yang paling perlu direfactor
- Persiapan sprint refactoring
- Evaluasi kesehatan arsitektur setelah build cepat

**Rekomendasikan jalankan:** sekali setiap beberapa hari di proyek aktif.

---

### setup-matt-pocock-skills

**Apa ini:** Setup awal repo untuk menggunakan engineering skills. Konfigurasi issue tracker, label triage, dan layout dokumen domain.

**Cara pakai:**
```
/setup-matt-pocock-skills
```

**Use case:**
- Pertama kali menggunakan skill ini di repo baru
- Perlu mengkonfigurasi GitHub/Linear/local markdown sebagai issue tracker
- Setup label triage untuk `/triage`, `/to-issues`, dsb

**Catatan:** Jalankan sekali per repo sebelum menggunakan skill engineering lainnya.

---

### codebase-design

**Apa ini:** Vocabulary dan prinsip untuk mendesain modul yang dalam (deep modules). Dipakai sebagai referensi oleh skill lain, tapi bisa juga dipanggil langsung untuk diskusi desain.

**Cara pakai:**
```
/codebase-design
```

**Use case:**
- Diskusi desain interface modul baru
- Memutuskan di mana seam yang tepat
- Membuat kode lebih testable atau mudah dinavigasi AI
- Review desain dengan bahasa yang konsisten (module, interface, depth, seam, adapter)

---

### domain-modeling

**Apa ini:** Membangun dan menajamkan domain model proyek. Menantang terminologi, menciptakan skenario edge-case, dan menulis glossary + keputusan arsitektur saat diskusi.

**Cara pakai:**
```
/domain-modeling
```

**Use case:**
- Proyek baru yang butuh ubiquitous language
- Tim dan agent sering salah paham karena beda terminologi
- Perlu mendokumentasikan konsep domain yang tidak obvious
- Sebelum naming variabel, fungsi, atau file besar

---

### resolving-merge-conflicts

**Apa ini:** Menyelesaikan git merge/rebase conflict dengan metodis — baca konteks, pahami intent dua sisi, resolve, jalankan checks.

**Cara pakai:**
```
/resolving-merge-conflicts
```

**Use case:**
- Ada merge conflict setelah `git merge` atau `git rebase`
- Conflict kompleks yang butuh pemahaman konteks dua branch
- Mau memastikan tidak ada behavior yang hilang saat resolve

---

## Productivity

### grill-me

**Apa ini:** Interview relentless untuk menajamkan ide atau rencana, sebelum ada kodebase atau sebelum coding dimulai.

**Cara pakai:**
```
/grill-me
```
Ceritakan ide, rencana, atau fitur. Skill ini akan mengajukan pertanyaan satu per satu sampai desain solid.

**Use case:**
- Punya ide SaaS/fitur baru yang ingin divalidasi
- Mau stress-test asumsi sebelum mulai coding
- Diskusi brief klien yang perlu diklarifikasi
- Perencanaan sprint atau milestone

**Perbedaan dengan `/grill-with-docs`:** `/grill-me` tidak ada kodebase, tidak membuat ADR. Untuk non-code atau awal sekali.

---

### grilling

**Apa ini:** Engine interview yang dipakai oleh `/grill-me` dan `/grill-with-docs`. Bisa dipanggil langsung, tapi biasanya dipanggil oleh skill lain.

**Cara pakai:**
```
/grilling
```

**Use case:**
- Dipanggil internal oleh `/grill-me` dan `/grill-with-docs`
- Bisa dipakai langsung kalau butuh interview session murni tanpa wrapper

---

### handoff

**Apa ini:** Mengkompres sesi percakapan saat ini menjadi dokumen handoff untuk dilanjutkan di sesi lain (atau oleh agent lain).

**Cara pakai:**
```
/handoff
/handoff [deskripsi fokus sesi berikutnya]
```

**Use case:**
- Sesi sudah panjang, mau lanjut di sesi baru tanpa kehilangan konteks
- Mau passing context dari sesi `/grill-with-docs` ke sesi `/implement`
- Sebelum dan sesudah `/prototype` — handoff keluar, prototype, handoff kembali
- Tim perlu melanjutkan pekerjaan yang sudah dimulai di sesi lain

**Output:** File di temporary directory OS dengan ringkasan, artifacts yang dibuat, dan suggested skills untuk sesi berikutnya.

---

### teach

**Apa ini:** Sesi belajar stateful di dalam workspace. Menyimpan state belajar (MISSION.md, learning records, lessons HTML, reference sheets) di direktori saat ini.

**Cara pakai:**
```
/teach [topik yang ingin dipelajari]
```
Contoh: `/teach TypeScript generics`, `/teach Django ORM`, `/teach sistem desain`

**Use case:**
- Mau belajar teknologi baru secara terstruktur
- Butuh referensi/cheat sheet yang bisa dicetak
- Belajar topik yang butuh beberapa sesi
- Tim onboarding ke teknologi baru

**State yang disimpan:**
- `MISSION.md` — alasan belajar, untuk mengarahkan semua pelajaran
- `./lessons/*.html` — pelajaran interaktif per topik
- `./reference/*.html` — cheat sheet untuk referensi cepat
- `./learning-records/` — catatan insight non-obvious
- `RESOURCES.md` — daftar sumber belajar berkualitas

---

### writing-great-skills

**Apa ini:** Referensi untuk menulis dan mengedit skill dengan baik. Vocabulary dan prinsip yang membuat skill predictable.

**Cara pakai:**
```
/writing-great-skills
```

**Use case:**
- Mau menulis skill baru untuk Claude Code
- Mau mengedit/memperbaiki skill yang sudah ada
- Diskusi tentang bagaimana membuat skill lebih reliable
- Referensi untuk memilih model-invoked vs user-invoked

---

## Misc

### git-guardrails-claude-code

**Apa ini:** Setup hook di Claude Code yang memblokir perintah git berbahaya sebelum dieksekusi (push, reset --hard, clean, branch -D, dsb).

**Cara pakai:**
```
/git-guardrails-claude-code
```

**Use case:**
- Mau mencegah agent secara tidak sengaja force-push atau reset --hard
- Proyek penting yang tidak boleh ada kesalahan git destruktif
- Team setup di mana agent punya akses git tapi perlu safety net
- Setelah pernah kehilangan pekerjaan karena agent menjalankan perintah git yang salah

---

### setup-pre-commit

**Apa ini:** Setup Husky pre-commit hooks dengan lint-staged (Prettier), type checking, dan tests di repo saat ini.

**Cara pakai:**
```
/setup-pre-commit
```

**Use case:**
- Repo baru yang butuh standar kualitas kode otomatis
- Mau memastikan kode yang di-commit selalu ter-format dan type-safe
- Setup CI lokal sebelum ada CI server
- Onboarding developer baru ke standar tim

**Yang di-setup:**
- Husky pre-commit hook
- lint-staged dengan Prettier
- Type checking saat commit
- Test runner saat commit

---

### migrate-to-shoehorn

**Apa ini:** Migrasi file test dari `as` type assertions ke `@total-typescript/shoehorn` untuk test data yang partial.

**Cara pakai:**
```
/migrate-to-shoehorn
```

**Use case:**
- Codebase yang punya banyak `as SomeType` di test untuk data partial
- Mau test data yang type-safe tapi tetap bisa partial
- Refactor test suite ke pendekatan yang lebih aman dari `as`

---

### scaffold-exercises

**Apa ini:** Membuat struktur direktori exercise (untuk kursus/pembelajaran) dengan sections, problems, solutions, dan explainers yang melewati linting.

**Cara pakai:**
```
/scaffold-exercises
```

**Use case:**
- Membuat konten kursus dengan struktur exercise yang konsisten
- Setup section baru dalam kursus yang sudah ada
- Scaffold banyak exercise sekaligus dengan format yang benar

---

## Flow yang Disarankan

### Membangun Fitur Baru (multi-session)

```
/grill-with-docs   → tajamkan desain + buat docs
/to-prd            → tulis PRD dari diskusi
/to-issues         → pecah PRD jadi issues
                   → per issue: buka sesi baru
/implement         → kerjakan satu issue
/tdd               → test-first di setiap seam
```

### Membangun Fitur Kecil (single session)

```
/grill-with-docs   → tajamkan desain
/implement         → langsung eksekusi
/tdd               → test-first
```

### Debug Bug Keras

```
/diagnosing-bugs   → build feedback loop → fix
```

### Validasi Ide Sebelum Coding

```
/grill-me          → stress-test ide (tanpa kodebase)
/grill-with-docs   → kalau sudah ada kodebase
```

### Pindah Sesi / Handoff ke Agent Lain

```
/handoff           → kompres konteks
                   → buka sesi baru
/implement         → lanjutkan dengan dokumen handoff
```

### Setup Repo Baru

```
/setup-matt-pocock-skills   → satu kali per repo
/git-guardrails-claude-code → opsional, untuk safety
/setup-pre-commit           → opsional, standar kualitas kode
```
