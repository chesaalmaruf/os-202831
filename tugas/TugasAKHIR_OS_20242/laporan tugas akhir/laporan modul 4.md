# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi  
**Semester**: Genap / Tahun Ajaran 2024–2025  
**Nama**: ` CHESA SALSABIL AL'MA'RUF `
**NIM**: `240202831` 
**Modul yang Dikerjakan**:  
`Modul 4 – Subsistem Kernel Alternatif (chmod() dan /dev/random)`

---

## 📌 Deskripsi Singkat Tugas

Modul ini menambahkan dua fitur baru ke sistem operasi `xv6-public` sebagai bagian dari eksplorasi subsistem kernel lanjutan:

1. **System Call `chmod(path, mode)`**  
   Digunakan untuk mengatur mode akses file menjadi *read-only* (`mode = 1`) atau kembali ke *read-write* (`mode = 0`).
1. **System Call `chmod(path, mode)`**  
   Digunakan untuk mengatur mode akses file menjadi *read-only* (`mode = 1`) atau kembali ke *read-write* (`mode = 0`).

2. **Device File `/dev/random`**  
   Sebuah *character device* yang menghasilkan data acak setiap kali dibaca, layaknya `/dev/random` pada sistem UNIX modern.

---

## 🛠️ Rincian Implementasi

### 🔒 Fitur `chmod(path, mode)`

- Menambahkan implementasi syscall `chmod()` di `sysfile.c`.
- Menambahkan field `mode` pada struktur `inode` di `fs.h` untuk menyimpan status izin akses file.
- Memodifikasi fungsi `filewrite()` di `file.c` agar memeriksa nilai `f->ip->mode`. Jika `mode == 1`, maka operasi tulis akan diblokir.
- Mendaftarkan syscall ke dalam:
  - `syscall.c`
  - `syscall.h`
  - `user.h`
  - `usys.S`
- Menambahkan program uji `chmodtest.c`.

### 🔄 Fitur `/dev/random`

- Membuat file baru `random.c` yang berisi fungsi `randomread()` untuk menghasilkan byte acak.
- Mendaftarkan device driver ke dalam `devsw[]` di `file.c`.
- Menambahkan node `/dev/random` dengan `mknod()` di `init.c`.
- Menambahkan program uji `randomtest.c`.

---

## 📁 Daftar File yang Diubah / Ditambahkan

Berikut adalah file yang diubah atau ditambahkan dalam implementasi Modul 4:

- `Makefile`  
- `chmodtest.c`  
- `file.c`  
- `file.h`  
- `init.c`  
- `random.c`  
- `randomtest.c`  
- `syscall.c`  
- `syscall.h`  
- `sysfile.c`  
- `user.h`  
- `usys.S`

---

## ✅ Pengujian Fungsionalitas

Dua program uji disusun untuk menguji kedua fitur utama:

### **1. chmodtest**  
Digunakan untuk memastikan bahwa file yang telah diubah menjadi *read-only* tidak dapat ditulis ulang oleh proses lain.

**Output :**
````
Write blocked as expected
````

### **2. randomtest**  
Digunakan untuk memverifikasi bahwa `/dev/random` menghasilkan byte acak setiap kali dibaca.

**Output:**
````
161 99 65 195 225 35 129
````



---

## ⚠️ Kendala yang Dihadapi

1. **Nilai Mode Tidak Persisten**  
   Field `mode` pada inode tidak disimpan ke disk sehingga nilainya hilang setelah reboot.

2. **Device Tidak Terdeteksi**  
   Terlupa menambahkan perintah `mknod("/dev/random", ...)` menyebabkan file device tidak dibuat sehingga `open()` gagal.

3. **Kesalahan Indeks pada `devsw[]`**  
   Jika posisi driver dalam array `devsw[]` tidak sesuai, maka fungsi pembacaan `randomread()` tidak dipanggil.

4. **Output Acak Tidak Bervariasi**  
   Menggunakan seed acak yang tetap (statis) menyebabkan data yang dihasilkan selalu sama setiap boot.

---

## 📚 Referensi

- modul github repository: https://github.com/Mhmmdfthn
- MODUL PRAKTIKUM oleh dosen pembimbing, github repository :
https://github.com/mhbahara/os-202402
- problem solving di chatgpt

