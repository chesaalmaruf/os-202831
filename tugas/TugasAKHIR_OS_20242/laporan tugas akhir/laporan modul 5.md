# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi  
**Semester**: Genap / Tahun Ajaran 2024–2025  
**Nama**: `CHESA SALSABIL AL'MA'RUF`  
**NIM**: `240202831`  
**Modul yang Dikerjakan**:  
`Modul 5 – Audit dan Keamanan Sistem`

---

## 📌 Deskripsi Singkat Tugas

Menambahkan sistem audit pada kernel xv6 yang mencatat setiap pemanggilan system call oleh proses. Audit log hanya dapat diakses oleh proses dengan PID 1 (init). Tujuannya untuk keamanan dan monitoring aktivitas sistem.

---

## 🛠️ Rincian Implementasi

- **`audit.c`**: File baru berisi `record_audit()` dan buffer `audit_log[]`
- **`sysproc.c`**: Tambahkan system call `sys_get_audit_log()` untuk mengambil data audit
- **`syscall.c`**, **`syscall.h`**, **`user.h`**, **`usys.S`**: Tambahkan syscall `get_audit_log`
- **`defs.h`**: Tambahkan deklarasi `record_audit()` dan `struct audit_entry`
- **`Makefile`**: Tambahkan rule build untuk `audittest`
- Audit hanya dapat diakses oleh proses dengan `pid == 1`, jika tidak maka akses akan ditolak

---

## 📁 File yang Diubah

- `Makefile`  
- `audit.c`  
- `defs.h`  
- `syscall.c`  
- `syscall.h`  
- `sysproc.c`  
- `user.h`  
- `usys.S`

---

## 🔎 Hasil Uji

Berikut adalah hasil tampilan saat perintah `audit` dijalankan di dalam shell xv6:

![Hasil Audit Log](hasil.%20modul%205.png)

**Cuplikan isi audit log**:
````
=== Audit Log ===
[0] PID=1 SYSCALL=7 TICK=4
[1] PID=1 SYSCALL=15 TICK=10
[2] PID=1 SYSCALL=17 TICK=10
[3] PID=1 SYSCALL=16 TICK=15
[4] PID=1 SYSCALL=15 TICK=15
...
[21] PID=1 SYSCALL=16 TICK=16
````

✅ **Berhasil** mencatat aktivitas system call dari proses PID 1.  
❌ Jika `audit` dijalankan oleh proses selain PID 1, maka output:  
````
Access denied or error.
````

---

## ⚠️ Kendala yang Dihadapi

- Lupa mendeklarasikan syscall baru di `user.h` dan `syscall.h`
- Salah akses PID dari `myproc()` menyebabkan akses log tidak dibatasi
- Program uji `audittest` tidak dikompilasi karena belum ditambahkan ke `Makefile`

---

## 📚 Referensi

- xv6-public github repository: https://github.com/mhbahara/os-202402
- Diskusi praktikum dan debugging error menggunakan ChatGPT  





