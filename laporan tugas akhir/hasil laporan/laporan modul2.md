# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi  
**Semester**: Genap / Tahun Ajaran 2024–2025  
**Nama**: `<Nama Lengkap>`  
**NIM**: `<Nomor Induk Mahasiswa>`  
**Modul yang Dikerjakan**:  
Modul 2 – Penjadwalan CPU Lanjutan (Priority Scheduling Non-Preemptive)

---

## 📌 Deskripsi Singkat Tugas

Modul ini bertujuan mengubah algoritma penjadwalan proses di kernel xv6 dari model default Round Robin menjadi Non-Preemptive Priority Scheduling, dengan:

- Menambahkan field `priority` pada struktur proses
- Membuat syscall baru `set_priority(int)`
- Memodifikasi fungsi `scheduler()` agar memilih proses dengan prioritas tertinggi (angka prioritas terkecil)

---

## 🛠️ Rincian Implementasi

Berikut langkah-langkah implementasi:

- `proc.h`:  
  Menambahkan field `int priority` ke `struct proc`

- `proc.c`:  
  - Menambahkan `p->priority = 60` di `allocproc()`
  - Memodifikasi `scheduler()` menjadi pemilih proses `RUNNABLE` dengan prioritas numerik terkecil menggunakan `mycpu()` dan `c->proc`

- `syscall.h`:  
  Menambahkan syscall number:
  ```c
  #define SYS_set_priority 24
  ```
-`user.h:`menambahkan deklarasi:
```
int set_priority(int);
```
-`usys.S` Menambahkan syscall entry:
```
SYSCALL(set_priority)
```
-`syscall.c`
tambah:
```
extern int sys_set_priority(void);
```
registrasi:
```
[SYS_set_priority] sys_set_priority,
```
-`sysproc.c` Menambahkan implementasi syscall:
```
int sys_set_priority(void) {
  int prio;
  if (argint(0, &prio) < 0 || prio < 0 || prio > 100)
    return -1;
  myproc()->priority = prio;
  return 0;
}
```
Makefile: 
```
_ptest
```
✅ Uji Fungsionalitas
Program uji ptest.c berisi:
fork() dua proses
Child 1 diberi prioritas 90
Child 2 diberi prioritas 10
Masing-masing menjalankan perulangan berat (busy())
Ouput menunjukkan bahwa proses prioritas lebih tinggi selesai duluan

## HASIL UJI
```
$ prest
Child 1 selesai
Child 2 selesai
Parent selesai
$
```
## 🛠️ md2 – Kendala yang Dihadapi

Berikut adalah kendala teknis yang dihadapi selama implementasi Modul 2 dan cara penyelesaiannya:

1. **`conflicting types for 'ptable'`**  
   🔹 Terjadi karena `struct ptable` didefinisikan berbeda antara `proc.h` dan `proc.c`.  
   ✅ Solusi: Samakan definisi dengan membuat `struct ptable { ... };` di `proc.h` dan deklarasikan `extern struct ptable ptable;`.

2. **`invalid use of undefined type 'struct ptable'`**  
   🔹 Terjadi karena `struct proc` belum didefinisikan saat digunakan di `struct ptable`.  
   ✅ Solusi: Pastikan `struct proc` dideklarasikan terlebih dahulu di `proc.h`.

3. **`proc` dan `cpu` undeclared di `scheduler()`**  
   🔹 Variabel global tidak dikenal karena tidak dideklarasikan.  
   ✅ Solusi: Gunakan `mycpu()` dan `c->proc` dari `defs.h`, sesuai dengan arsitektur xv6.

4. **`multiple definition of 'cpus'`**  
   🔹 Terjadi karena `struct cpu cpus[NCPU]` didefinisikan ulang di `proc.c`.  
   ✅ Solusi: Hapus definisi tersebut dan gunakan `extern struct cpu cpus[NCPU];` bila diperlukan.

5. **`gas: No such file or directory`**  
   🔹 Terjadi karena `gas` (GNU Assembler) tidak ditemukan oleh Makefile.  
   ✅ Solusi: Buat symbolic link `ln -sf /usr/bin/as /usr/bin/gas`.

6. **`gas: unrecognized option '-m32'`**  
   🔹 Karena toolchain 32-bit belum tersedia di sistem.  
   ✅ Solusi: Jalankan `apt install gcc-multilib g++-multilib libc6-dev-i386` untuk mengaktifkan dukungan 32-bit.

Semua error di atas berhasil diselesaikan dan proses build dapat berjalan hingga sistem xv6 dapat diuji dengan sukses.

---
##kesimpulan
Kesimpulan:
Sistem xv6 berhasil melakukan booting dengan normal, termasuk inisialisasi CPU, disk, dan filesystem. Superblock terkonfigurasi dengan parameter yang sesuai, dan sistem siap menerima perintah.
---

## refrensi
-github repository: https://github.com/mhbahara/os-202402
- chat gpt : https://chatgpt.com/



