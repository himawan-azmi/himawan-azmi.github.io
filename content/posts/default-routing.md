+++
date = '2025-08-22T08:40:04+07:00'
draft = true
title = 'Belajar Default Routing dengan Cisco Packet Tracer'
showToc = true
tocOpen = true
tocTitle = 'Daftar Isi'
+++

> Disclaimer!  
Tulisan ini memuat materi yang tidak sepenuhnya berasal dari penulis sendiri, melainkan dirangkum dari berbagai sumber tepercaya dan dibantu oleh kecerdasan buatan. Seluruh referensi yang digunakan telah dicantumkan di bagian akhir, sehingga pembaca dapat dengan mudah menelusuri dan mempelajari sumber aslinya.  

## Pengantar  

Bismillah.  
Tulisan kali ini, akan melanjutkan seri belajar jaringan komputer. Setelah belajar tentang perutean statis, artikel kali ini akan membahas tentang perutean _default_ (_default routing_). Alat yang digunakan masih dengan alat simulasi jaringan _Cisco Packet Tracer_.

---

## Pendahuluan

### Apa itu _Default Routing_ (Perutean _Default_) ?  

_Default Routing_ atau perutean _default_ adalah metode perutean di mana sebuah jalur tunggal (_default route_) ditentukan untuk mengarahkan semua paket yang tujuannya tidak ditemukan dalam tabel _routing_.  
Dengan kata lain, kalau _router_ tidak tahu harus lewat mana, dia akan mengirimkan paket ke _default gateway_.

### Karakteristik _Default Routing_

- Digunakan sebagai “jalan keluar terakhir” (_gateway of last resort_).
- Biasanya dipakai di:
    - Jaringan kecil atau sederhana (misalnya kantor/rumah → ISP).
    - Host yang hanya punya satu jalur keluar (misalnya komputer _client_).
- _Router_ akan meneruskan paket ke _default route_ jika tidak ada rute yang lebih spesifik di tabel.

### Kelebihan

- Mudah dikonfigurasi, terutama untuk _host_ atau jaringan kecil.
- Efisien: Tidak perlu menyimpan semua rute di tabel.
- Wajib ada di _client_ biasa (misalnya PC/laptop) agar bisa keluar ke _Internet_.

### Kekurangan

- Tidak cocok untuk jaringan besar yang butuh rute spesifik
- Tidak optimal jika ada banyak jalur keluar (bisa salah pilih rute).
- Jika _default gateway_ gagal → paket tidak bisa dikirim ke luar jaringan.

---

## Praktik

### Persiapan Alat dan Bahan

Alat dan bahan yang digunakan dalam praktik belajar perutean _default_ yaitu:
1. Alat:
    - Aplikasi ***Cisco Packet Tracer***
2. Bahan:
    - Topologi Jaringan

        ![Topologi jaringan](/img/default-routing/topologi-default-routing.png)

    - Tabel Pengalamatan (_Addressing Table_)

    | Jenis Perangkat | Nama Perangkat | _Interface_ | IP _Address_ | _Subnet Mask_   | _Gateway_ |
    |-----------------|----------------|-------------|--------------|-----------------|-----------|
    | Router          |     R1         | Gig0/0      | 10.10.10.1   | 255.255.255.248 | -         |
    |                 |     R1         | Gig0/1      | 192.168.10.6 | 255.255.255.248 | -         |
    |                 |     R2         | Gig0/0      | 10.10.10.2   | 255.255.255.248 | -         |
    |                 |     R2         | Gig0/1      | 192.168.20.6 | 255.255.255.248 | -         |
    | Switch          |     SW1        | Fa0/0       | N/A          | N/A             | -         |
    |                 |     SW1        | Fa0/1       | N/A          | N/A             | -         |
    |                 |     SW2        | Fa0/0       | N/A          | N/A             | -         |
    |                 |     SW2        | Fa0/1       | N/A          | N/A             | -         |
    |Personal Computer|     PC1        | Fa0         | 192.168.10.1 | 255.255.255.248 | -         |
    |                 |     PC2        | Fa0         | 192.168.20.1 | 255.255.255.248 | -         |

---

### Konfigurasi

Konfigurasi dilakukan secara bertahap mulai dari perangkat ***router*** sampai dengan ***personal computer***.
1. ***Router 1 (R1)***
    ***a. Masuk ke mode terminal IOS CLI (_Command Line Interface_)***  
    ***b. Nonaktifkan _IP Domain Lookup_***
    ***c. Konfigurasi _interface_ Gig0/0 dengan perintah
    ```
    R1(config)# int gig0/0
    R1(config-if)# description Connection to R2
    R1(config-if)# ip add 10.10.10.1 255.255.255.248
    R1(config-if)# no shutdown
    R1(config-if)# exit
    ```

---

### Pengujian

---

## Penutup

---

## Lampiran

---

## Referensi

1. RFC 1812 – _Requirements for IPv4 Routers_ (lihat bagian tentang _default route_): https://www.rfc-editor.org/rfc/rfc1812
2. Odom, W. (2020). _CCNA 200-301 Official Cert Guide_. _Pearson IT Certification_.
4. Taufik, M. (2015). _The Way to Be Cisco Warrior: Teori & Praktik_. Jakarta: ID-Networkers.
5. Setiawan, A. (2015). _CCNA Lab Guide (Edisi Pertama)_. Bandung: Nixtrain.