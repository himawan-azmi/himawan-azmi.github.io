+++
date = '2025-08-22T08:40:04+07:00'
draft = false
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
1. **Router 1 (R1)**  
    **a. Masuk ke mode terminal IOS CLI (_Command Line Interface_)**  
    **b. Nonaktifkan _Domain Lookup_**
    **c. Konfigurasi _interface_ Gig0/0**
    ```
    R1(config)#int gig0/0
    R1(config-if)#description Connection to R2
    R1(config-if)#ip add 10.10.10.1 255.255.255.248
    R1(config-if)#no shutdown
    R1(config-if)#exit
    ```
    **d. Konfigurasi _Interface_ Gig0/1**
    ```
    R1(config)#int gig0/1
    R1(config-if)#description Connection to PC2
    R1(config-if)#ip add 192.168.10.6 255.255.255.248
    R1(config-if)#no shutdown
    R1(config-if)#exit
    ```  
    **e. Konfigurasi alamat perutean (_routing address_)**
    _Default route_ menggunakan _network address destination_ statis 0.0.0.0 dan _subnet mask_ 0.0.0.0 yang dikenal sebagai ***"quad zero"***.  
    **- Dengan _next-hop ip address_**  
    ```
    R1(config)#ip route 0.0.0.0 0.0.0.0 10.10.10.2
    ```
    **- Dengan _exit-interface_**
    ```
    R1(config)#ip route 0.0.0.0 0.0.0.0 gig0/0
    ```
    **f. Simpan konfigurasi yang sedang berjalan**  
    ```
    R1#copy run start
    ```
2. **Router 2 (R2)**  
    **a. Masuk ke mode terminal IOS CLI**
    **b. Nonaktifkan _Domain Lookup_**
    **c. Konfigurasi _interface Gig0/0_**
    ```
    R2(config)#int gig0/0
    R2(config-if)#description Connection to R1
    R2(config-if)#ip add 10.10.10.2 255.255.255.248
    R2(config-if)#no shutdown
    R2(config-if)#exit
    ```
    **d. Konfigurasi _interface Gig0/1_**  
    ```
    R2(config)#int gig0/1
    R2(config-if)#description Connection to PC1
    R2(config-if)#ip add 192.168.20.6 255.255.255.248
    R2(config-if)#no shutdown
    R2(config-if)#exit
    ```
    **e. Konfigurasi alamat perutean (_routing address_)**
    **- Dengan _next-hop ip address_**
    ```
    R2(config)#ip route 0.0.0.0 0.0.0.0 10.10.10.1
    ```
    **- Dengan _exit-interface_**  
    ```
    R2(config)#ip route 0.0.0.0 0.0.0.0 gig0/0
    ```
    **f. Simpan konfigurasi yang sedang berjalan**  
    ```
    R2#copy run start
    ```
3. **Personal Computer (PC)**
    - _IP Configuration_ PC1

    ![IP Configuration PC1](/img/default-routing/ip-config_pc1.png)

    ---

    - _IP Configuration_ PC2

    ![IP Configuration PC2](/img/default-routing/ip-config_pc2.png)

---

### Pengujian

1. Periksa konfigurasi perutean dengan perintah `show ip route`  

    ![Konfigurasi Perutean R1](/img/default-routing/show-ip-route_R1.png)

    ---

    ![Konfigurasi Perutean R2](/img/default-routing/show-ip-route_R2.png)

2. Lakukan ***ping*** antar _router_

    ![Ping R1 to R2](/img/default-routing/ping_R1.png)

    ---

    ![Ping R2 to R1](/img/default-routing/ping_R2.png)

3. Lakukan ***ping*** antar _personal computer_

    ![Ping PC1 to PC2](/img/default-routing/ping_pc1.png)

    ---

    ![Ping PC2 to PC1](/img/default-routing/ping_pc2.png)

4. Dengan ***simple PDU***

    ![Simple PDU](/img/default-routing/simple_PDU.png)

---

## Penutup

Setelah mempelajari konsep ***default routing*** beserta langkah-langkah praktik konfigurasinya, semoga tulisan ini dapat membantu teman-teman dalam memahami jaringan komputer sekaligus menumbuhkan semangat untuk terus belajar.  
Terima kasih telah berkenan membaca hingga akhir. Penulis memohon maaf apabila terdapat kekeliruan atau kekurangan dalam penyampaian materi. Sampai jumpa di tulisan berikutnya. Salam hangat!

---

## Lampiran

Untuk teman-teman yang tertarik untuk mendapatkan file praktik dari materi ini dapat mengunduhnya pada tautan berikut:
[default-routing.pkt](/files/default-routing/default-routing.pkt)

---

## Referensi

1. RFC 1812 – _Requirements for IPv4 Routers_ (lihat bagian tentang _default route_): https://www.rfc-editor.org/rfc/rfc1812
2. Odom, W. (2020). _CCNA 200-301 Official Cert Guide_. _Pearson IT Certification_.
3. Taufik, M. (2015). _The Way to Be Cisco Warrior: Teori & Praktik_. Jakarta: ID-Networkers.
4. Setiawan, A. (2015). _CCNA Lab Guide (Edisi Pertama)_. Bandung: Nixtrain.