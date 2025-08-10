+++
date = '2025-08-06T22:16:23+07:00'
draft = true
title = 'Belajar Static Routing dengan Cisco Packet Tracer'
showToc= true
tocOpen= true
tocTitle= "Daftar Isi"
+++

[!CAUTION]  
> Tulisan ini memuat materi yang tidak sepenuhnya berasal dari penulis sendiri, melainkan dirangkum dari berbagai sumber tepercaya dan dibantu oleh kecerdasan buatan. Seluruh referensi yang digunakan telah dicantumkan di bagian akhir, sehingga pembaca dapat dengan mudah menelusuri dan mempelajari sumber aslinya.

## Pengantar

Bismillah.  
Pada kesempatan kali ini, saya akan berbagi panduan bagaimana cara melakukan perutean statis (_static routing_) dengan alat simulasi jaringan _Cisco Packet Tracer_. Tulisan ini, akan menjadi awal dari seri belajar jaringan komputer. Ada kemungkinan tulisan-tulisan yang nanti terbit tidak akan berurutan. Tetapi, harapannya tetap bisa memberi manfaat dan membantu teman-teman dalam belajar jaringan komputer.

---

## Pendahuluan

### Apa itu _Static Routing_ (Perutean Statis)
_Static Routing_ merupakan metode perutean di mana jalur (_route_) dari suatu jaringan ke jaringan lain ditentukan dan dikonfigurasi secara manual oleh administrator jaringan, tanpa melibatkan protokol perutean dinamis.  
Dalam _static routing_, informasi rute dimasukkan langsung ke _routing table_ oleh admin, dan rute tersebut tetap digunakan sampai diubah atau dihapus secara manual, meskipun kondisi jaringan berubah.  

### Karakteristik _Static Routing_  
- Manual: Ditambahkan atau dihapus secara eksplisit oleh admin.  
- Tidak adaptif: Tidak otomatis menyesuaikan jika terjadi perubahan topologi jaringan.  
- Sederhana: Cocok untuk jaringan kecil dengan sedikit jalur.  
- Prediktif: Jalur yang digunakan selalu sama, sehingga mudah dipantau.

### Kelebihan  
- Lebih hemat _resource_ (CPU & memori) dibanding _dynamic routing_.  
- Lebih aman karena tidak menerima update rute dari _router_ lain.  
- Prediksi jalur lebih mudah.  

### Kekurangan  
- Sulit dikelola di jaringan besar.  
- Tidak otomatis mengalihkan rute jika jalur gagal (harus manual).

---

## Praktik

### Persiapan Alat dan Bahan
Sebelum melakukan praktik, hal pertama yang perlu dilakukan adalah mempersiapkan alat dan bahan. Adapun alat dan bahan yang akan digunakan dalam praktik belajar perutean statis ini yaitu:
1. Alat: 
    - Aplikasi ***Cisco Packet Tracer***
2. Bahan:
    - Topologi
        [image]

    - Tabel Pengalamatan (_Addressing Table_)

    | Jenis Perangkat | Nama Perangkat | _Interface_ | IP _Address_     | _Subnet Mask_   | _Gateway_     |
    |-----------------|----------------|-------------|------------------|-----------------|---------------|
    |      Router     |     R1         |    Gig0/0   |  10.10.10.1      | 255.255.255.248 |     -         |
    |                 |     R1         |    Gig0/1   |  192.168.100.6   | 255.255.255.248 |     -         |
    |                 |     R2         |    Gig0/0   |  10.10.10.2      | 255.255.255.248 |     -         |
    |                 |     R2         |    Gig0/1   |  192.168.200.6   | 255.255.255.248 |     -         |
    |    Switch       |     SW1        |    Fa0/0    |         -        |   N/A           |     -         |
    |                 |     SW2        |    Fa0/1    |         -        |   N/A           |     -         |
    |       PC        |     PC1        |    Gig/1    | 192.168.100.1    | 255.255.255.248 | 192.168.100.6 |
    |                 |     PC2        |    Gig/1    | 192.168.200.1    | 255.255.255.248 | 192.168.200.6 |

---    

### Konfigurasi
Proses konfigurasi dilakukan secara bertahap satu per satu dimulai dari perangkat ***Router*** sampai dengan perangkat komputer (PC).
1. **Router 1 (R1)**  
    **a. Masuk ke mode terminal perangkat atau IOS _Command Line Interface_(CLI).**  
    **b. Nonaktifkan _Domain Lookup_.**  
        Saat pertama kali melakukan konfigurasi perangkat _router_, nonaktifkan (_disable_) fitur ***Domain Lookup*** untuk mencegah pencarian DNS (_Domain Name System_) secara otomatis oleh _router_ ketika terjadi kesalahan penulisan perintah (_typo_) atau penulisan perintah yang tidak selesai.  
    **- Masuk ke _Privileged Mode_ dari _User Mode_ dengan perintah berikut:**  
    ```
    Router> enable  
    ```  
    **- Setelah masuk ke _Privileged Mode_, masuk ke _Global Configuration Mode_ dengan perintah berikut:**  
    ```
    Router# config terminal
    ```  
    **- Mengubah nama host (_hostname_) dengan perintah berikut:**  
    ```
    Router(config)# hostname R1
    ```  
    **- Nonaktifkan _domain-lookup_ dengan perintah berikut:**  
    ```
    R1(config)# no ip domain-lookup
    ```  
    **c. Konfigurasi alamat IP (IP _Address_) untuk _interface_ Gig0/0 seperti berikut:**  
    ```
    R1(config)# int gig0/0
    R1(config)# description Connection to R2
    R1(config)# ip address 10.10.10.1 255.255.255.248
    R1(config)# no shutdown
    R1(config)# exit
    ```
    **d. Konfigurasi alamat IP (_IP Address_) untuk _interface_ Gig0/1 seperti berikut:**  
    ```
    R1(config)# int gig0/1
    R1(config)# description Connection to PC2
    R1(config)# ip address 192.168.100.6 255.255.255.248
    R1(config)# no shutdown
    R1(config)# exit
    ```
    **e. Konfigurasi alamat perutean (_routing address_)**  
        Untuk melakukan konfigurasi perutean, dapat dilakukan dengan dua cara yaitu _next-hop_ IP _address_ dan _exit-interface_.  
    **- _Next-hop_ IP _Address_**  
    ```
    ip route <network-destination> <subnet-mask network-destination> <next-hop ip address>
    ```  
    ```
    R1(config)# ip route 192.168.200.0 255.255.255.248 10.10.10.2
    ```  
    **- _Exit-interface_**  
    ```
    ip route <network-destination> <subnet-mask network-destination> <exit-interface>
    ```  
    ```
    R1(config)# ip route 192.168.200.0 255.255.255.248 gig0/1
    ```  
    **f. Simpan konfigurasi yang sedang berjalan**  
        Untuk menghindari hilangnya konfigurasi yang diterapkan saat ini, perlu untuk menyimpannya di memori. Perangkat yang dimuat ulang (_reboot_) akan menghapus konfigurasi yang tidak disimpan di memori. Konfigurasi yang tersimpan akan dimuat oleh perangkat ketika perangkat memulai sistem.  
    ```
    R1# copy running-config startup-config
    ```
2. **Router 2 (R2)**  
    **a. Masuk ke mode terminal (CLI) perangkat.**  
    **b. Nonaktifkan _Domain Lookup_.**  
        Sama seperti langkah kedua konfigurasi ***Router 1*** sebelumnya yaitu menonaktifkan fitur _Domain Lookup_.  
    **- Masuk ke _Privileged Mode_ dari _User Mode_ dengan perintah berikut:**  
    ```
    Router> enable
    ```  
    **- Setelah masuk ke _Privileged Mode_, masuk ke _Global Configuration Mode_ dengan perintah berikut:**  
    ```
    Router# config terminal
    ```  
    **- Mengubah nama host (_hostname_) dengan perintah berikut:**  
    ```
    Router(config)# hostname R2
    ```  
    **- Nonaktifkan _domain-lookup_ dengan perintah berikut:**  
    ```
    R2(config)# no ip domain-lookup
    ```  
    **c. Konfigurasi alamat IP (IP _Address_) untuk _interface_ Gig0/0 seperti berikut:**  
    ```
    R2(config)# int gig0/0
    R2(config)# description Connection to R1
    R2(config)# ip address 10.10.10.2 255.255.255.248
    R2(config)# no shutdown
    R2(config)# exit
    ```
    **d. Konfigurasi alamat IP (_IP Address_) untuk _interface_ Gig0/1 seperti berikut:**  
    ```
    R2(config)# int gig0/1
    R2(config)# description Connection to PC1
    R2(config)# ip address 192.168.200.6 255.255.255.248
    R2(config)# no shutdown
    R2(config)# exit
    ```
    **e. Konfigurasi alamat perutean (_routing address_)**  
        Untuk melakukan konfigurasi perutean, dapat dilakukan dengan dua cara yaitu _next-hop_ IP _address_ dan _exit-interface_.  
    **- _Next-hop_ IP _Address_**  
    ```
    ip route <network-destination> <subnet-mask network-destination> <next-hop ip address>
    ```  
    ```
    R2(config)# ip route 192.168.100.0 255.255.255.248 10.10.10.1
    ```  
    **- _Exit-interface_**  
    ```
    ip route <network-destination> <subnet-mask network-destination> <exit-interface>  
    ```
    ```
    R2(config)# ip route 192.168.100.0 255.255.255.248 gig0/1
    ```  
    **f. Simpan konfigurasi yang berjalan saat ini**  
    ```
    R2# copy running-config startup-config
    ```
3. **Personal Computer 1 (PC1)**  

---

### Pengujian  
1. Periksa konfigurasi perutean  
    [image]
2. Lakukan ***ping*** antar _router_  
    [image]
3. Lakukan ***ping*** antar PC  
    [image]
4. Dengan ***simple PDU***  
    [image]
---

## Penutup

---

## Lampiran


---

## Referensi  
1. Cisco Networking Academy: https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html  
2. RFC 1812 – Requirements for IP Version 4 Routers: https://www.rfc-editor.org/rfc/rfc1812  
3. Tanenbaum, A. S., & Wetherall, D. J. (2011). _Computer Networks (5th ed.)_. Pearson.
4. Taufik, M. (2015). _The Way to Be Cisco Warrior: Teori & Praktik_. Jakarta: ID-Networkers.
5. Setiawan, A. (2015). _CCNA Lab Guide (Edisi Pertama)_. Bandung: Nixtrain.