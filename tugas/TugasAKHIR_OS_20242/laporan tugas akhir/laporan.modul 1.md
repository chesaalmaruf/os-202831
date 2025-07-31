# 📝 Laporan Tugas Akhir

*Mata Kuliah*: Sistem Operasi  
*Semester*: Genap / Tahun Ajaran 2024–2025  
*Nama*: `CHESA SALSABIL AL'MA'RUF` 
*NIM*: `240202831`
*Modul yang Dikerjakan*:  
Modul 1 – System Call dan Instrumentasi Kernel

---

## 📌 Deskripsi Singkat Tugas

Modul ini bertujuan untuk menambahkan dua system call baru pada kernel xv6-public:

- getpinfo(struct pinfo*) untuk mengambil informasi proses aktif (PID, ukuran memori, dan nama proses).
- getreadcount() untuk menghitung berapa kali fungsi read() dipanggil sejak sistem boot.

---

## 🛠 Rincian Implementasi

Berikut adalah langkah-langkah implementasi yang dilakukan:

- Menambahkan struktur struct pinfo di file proc.h
- Menambahkan variabel global readcount di sysproc.c
- Mendaftarkan dua system call baru getpinfo dan getreadcount di:
  - syscall.h (penambahan nomor syscall)
  - syscall.c (daftar syscall dan deklarasi fungsi)
  - user.h dan usys.S (deklarasi di sisi user)
- Mengimplementasikan dua fungsi syscall baru di sysproc.c
- Memodifikasi sysfile.c → fungsi sys_read() untuk menambahkan counter readcount++
- Membuat dua file uji:
  - ptest.c untuk menguji hasil getpinfo()
  - rtest.c untuk menguji hasil getreadcount()
- Menambahkan _ptest dan _rtest ke dalam Makefile

---

## ✅ Uji Fungsionalitas

Program uji yang digunakan:
```
$ ptest  
```
  Untuk menampilkan daftar proses aktif dan memverifikasi bahwa getpinfo() mengembalikan data yang valid.
```
$ rtest
```
  Untuk melihat nilai readcount sebelum dan sesudah memanggil read(), memastikan bahwa syscall getreadcount() berjalan dengan baik.

---

## hasil uji:
```
$ ptest
1 12288 init
2 16384 sh
3 12288 ptest
$ rtest
Before: 12
```

---

## ⚠ Kendala yang Dihadapi

Selama implementasi, beberapa kendala yang muncul adalah:

- *Conflicting types for 'ptable'*: kesalahan dalam mendefinisikan struct ptable baik di proc.h maupun proc.c. Solusinya adalah menyelaraskan definisi dan deklarasi menggunakan struct ptable { ... }; extern struct ptable ptable;
- *‘readcount’ undeclared*: karena readcount tidak dikenali di sysfile.c. Solusi: menggunakan extern int readcount; di file tersebut.
- *Redefinition of 'struct spinlock'*: akibat mendefinisikan struct spinlock dua kali. Solusi: gunakan #include "spinlock.h" dan pastikan hanya ada satu definisi struct spinlock.
- *Invalid use of undefined type ‘struct ptable’*: terjadi karena deklarasi tidak lengkap atau urutan definisi struct tidak benar.

Setelah menyelaraskan header dan urutan deklarasi, seluruh sistem berhasil dibuild dan dijalankan.

---

## 📚 Referensi

- github repository: https://github.com/mhbahara/os-202402 
- chat gpt: https://chatgpt.com/

---
