# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi  
**Semester**: Genap / Tahun Ajaran 2024–2025  
**Nama**: `CHESA SALSABIL AL'MA'RUF`  
**NIM**: `240202831`  
**Modul yang Dikerjakan**:  
`Modul 3 – Manajemen Memori Tingkat Lanjut (Copy-on-Write dan Shared Memory)`

---

## 📌 Deskripsi Singkat Tugas

* **Modul 3 – Manajemen Memori Tingkat Lanjut**:  
  Mengimplementasikan dua fitur utama dalam manajemen memori xv6:  
  1. **Copy-on-Write Fork (CoW)**: Optimalisasi `fork()` agar tidak langsung menggandakan page fisik. Page hanya dikloning saat ada penulisan pertama (write-once).  
  2. **Shared Memory System V**: Menambahkan system call `shmget()` dan `shmrelease()` yang memungkinkan dua proses berbagi page yang sama menggunakan sebuah key.

---

## 🛠️ Rincian Implementasi

### Copy-on-Write Fork

* Menambahkan field `ref_counters[]` untuk pelacakan reference count halaman fisik (di `vm.c`)
* Membuat fungsi `incr_ref()` dan `decr_ref()` untuk menambah/mengurangi refcount halaman
* Mengedit fungsi `fork()` di `proc.c` agar menggunakan `cowuvm()` daripada `copyuvm()`
* Menambahkan flag **COW** di entri page table menggunakan bit **PTE_COW**
* Menambahkan penanganan page fault (PF) di `trap.c` dengan fungsi `handle_cow_fault()`
* Jika proses menulis ke page yang di-share, maka dilakukan **alokasi page baru dan copy isi lama**

### Shared Memory System V

* Menambahkan struktur `struct shared_page` dan array `shared_pages[64]`
* Menambahkan dua system call baru:
  - `int shmget(int key)` – alokasikan atau akses shared memory berdasarkan key
  - `int shmrelease(int key)` – melepaskan shared memory
* Shared memory dimetakan di area `USERTOP - 1 page`, `USERTOP - 2 pages`, dst
* Mengedit `defs.h`, `syscall.c`, `syscall.h`, `usys.S`, dan `user.h` untuk mendukung syscall ini

---

## ✅ Uji Fungsionalitas

* `cowtest`: untuk menguji apakah proses child dan parent menggunakan page yang sama hingga ada penulisan
* `shmtest`: untuk menguji dua proses yang berbagi page via `shmget()`, kemudian menulis dan membaca

---

## 📷 Hasil Uji

### 📍 Output `cowtest`:

```
Child sees: X
Parent writes: Y
Child sees: X
Child writes: Z
Parent sees: Y
```

### 📍 Output `shmtest`:

```
Child reads: A
Parent reads: A
Parent writes: B
Child reads: B
```

---

## 🗂️ Daftar File yang Diubah / Ditambahkan

```
Laporan Modul 3.md       ← Laporan tertulis
Makefile                 ← Tambahan dependency uji (opsional)
cowtest.c                ← Program uji untuk Copy-on-Write
shmtest.c                ← Program uji untuk Shared Memory
defs.h                   ← Tambahan deklarasi syscall
mmu.h                    ← Tambahan flag PTE_COW
proc.c                   ← Modifikasi fungsi fork dan struktur proses
proc.h                   ← Tambahan field jika perlu
syscall.c                ← Menambahkan pemetaan syscall baru
syscall.h                ← Penambahan nomor syscall baru
sysproc.c                ← Implementasi syscall shmget dan shmrelease
trap.c                   ← Penanganan page fault untuk Copy-on-Write
user.h                   ← Deklarasi prototype syscall di user space
usys.S                   ← Tambahan entri untuk syscall baru
vm.c                     ← Implementasi fungsi cowuvm, shared memory, ref counter
```

---

## ⚠️ Kendala yang Dihadapi

* **Gagal kompilasi** karena duplikasi definisi `shared_pages` – solusi: gunakan `extern` dan pastikan definisi hanya sekali di file `.c`
* **panic: page fault** – karena handler `handle_cow_fault()` belum dipasang benar di `trap.c`
* **crash saat `kfree()`** – karena refcount tidak dilacak atau dibebaskan dua kali
* Salah shifting pada `pa >> 12` menyebabkan index ref counter keluar batas
* Penempatan flag COW harus sinkron antara user access dan write-protect

---

## 📚 Referensi

*- github repository: https://github.com/Mhmmdfthn
* Modul praktikum dan dokumentasi Dosen Asisten
* problep solving oleh chatGPT

