+++
date = '2025-08-06T22:16:23+07:00'
draft = false
title = 'Belajar Static Routing dengan Cisco Packet Tracer'
showToc= true
tocOpen= true
tocTitle= "Daftar Isi"
+++

> Disclaimer!  
Tulisan ini memuat materi yang tidak sepenuhnya berasal dari penulis sendiri, melainkan dirangkum dari berbagai sumber tepercaya dan dibantu oleh kecerdasan buatan. Seluruh referensi yang digunakan telah dicantumkan di bagian akhir, sehingga pembaca dapat dengan mudah menelusuri dan mempelajari sumber aslinya.

## Pengantar

Bismillah.  
Pada kesempatan kali ini, saya akan berbagi panduan bagaimana cara melakukan perutean statis (_static routing_) dengan alat simulasi jaringan _Cisco Packet Tracer_. Tulisan ini, akan menjadi awal dari seri belajar jaringan komputer. Ada kemungkinan tulisan-tulisan yang nanti terbit tidak akan berurutan. Tetapi, harapannya tetap bisa memberi manfaat dan membantu teman-teman dalam belajar jaringan komputer.

---

## Pendahuluan

### Apa itu _Static Routing_ (Perutean Statis) ?  

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
    - Topologi Jaringan  
    
        ![Topologi jaringan](/img/static-routing/topologi-static-routing_excalidraw.png)

    - Tabel Pengalamatan (_Addressing Table_)

    | Jenis Perangkat | Nama Perangkat | _Interface_ | IP _Address_     | _Subnet Mask_   | _Gateway_     |
    |-----------------|----------------|-------------|------------------|-----------------|---------------|
    |      Router     |     R1         |    Gig0/0   |  10.10.10.1      | 255.255.255.248 |     -         |
    |                 |     R1         |    Gig0/1   |  192.168.100.6   | 255.255.255.248 |     -         |
    |                 |     R2         |    Gig0/0   |  10.10.10.2      | 255.255.255.248 |     -         |
    |                 |     R2         |    Gig0/1   |  192.168.200.6   | 255.255.255.248 |     -         |
    |    Switch       |     SW1        |    Fa0/1    |       N/A        |   N/A           |     -         |
    |                 |     SW1        |    Fa0/2    |       N/A        |   N/A           |     -         |
    |                 |     SW2        |    Fa0/1    |       N/A        |   N/A           |     -         |
    |                 |     SW2        |    Fa0/2    |       N/A        |   N/A           |     -         |
    |       PC        |     PC1        |    Fa0      | 192.168.100.1    | 255.255.255.248 | 192.168.100.6 |
    |                 |     PC2        |    Fa0      | 192.168.200.1    | 255.255.255.248 | 192.168.200.6 |

---    

### Konfigurasi  

Proses konfigurasi dilakukan secara bertahap satu per satu dimulai dari perangkat ***Router*** sampai dengan perangkat komputer (PC).
1. **Router 1 (R1)**  
    **a. Masuk ke mode terminal perangkat atau IOS _Command Line Interface_(CLI).**  
    **b. Nonaktifkan _Domain Lookup_.**  
        Saat pertama kali melakukan konfigurasi perangkat _router_, nonaktifkan (_disable_) fitur ***Domain Lookup*** untuk mencegah pencarian DNS (_Domain Name System_) secara otomatis oleh _router_ ketika terjadi kesalahan penulisan perintah (_typo_) atau penulisan perintah yang tidak selesai.  
    **- Masuk ke _Privileged Mode_ dari _User Mode_ dengan perintah berikut:**  
    ```
    Router>enable  
    ```  
    **- Setelah masuk ke _Privileged Mode_, masuk ke _Global Configuration Mode_ dengan perintah berikut:**  
    ```
    Router#config terminal
    ```  
    **- Mengubah nama host (_hostname_) dengan perintah berikut:**  
    ```
    Router(config)#hostname R1
    ```  
    **- Nonaktifkan _domain-lookup_ dengan perintah berikut:**  
    ```
    R1(config)#no ip domain-lookup
    ```  
    **c. Konfigurasi alamat IP (IP _Address_) untuk _interface_ Gig0/0 seperti berikut:**  
    ```
    R1(config)#int gig0/0
    R1(config-if)#description Connection to R2
    R1(config-if)#ip address 10.10.10.1 255.255.255.248
    R1(config-if)#no shutdown
    R1(config-if)#exit
    ```
    **d. Konfigurasi alamat IP (_IP Address_) untuk _interface_ Gig0/1 seperti berikut:**  
    ```
    R1(config)#int gig0/1
    R1(config-if)#description Connection to PC2
    R1(config-if)#ip address 192.168.100.6 255.255.255.248
    R1(config-if)#no shutdown
    R1(config-if)#exit
    ```
    **e. Konfigurasi alamat perutean (_routing address_)**  
        Untuk melakukan konfigurasi perutean, dapat dilakukan dengan dua cara yaitu _next-hop_ IP _address_ dan _exit-interface_.  
    **- _Next-hop_ IP _Address_**  
    ```
    ip route <network-destination> <subnet-mask network-destination> <next-hop ip address>
    ```  
    ```
    R1(config)#ip route 192.168.200.0 255.255.255.248 10.10.10.2
    ```  
    **- _Exit-interface_**  
    ```
    ip route <network-destination> <subnet-mask network-destination> <exit-interface>
    ```  
    ```
    R1(config)#ip route 192.168.200.0 255.255.255.248 gig0/1
    ```  
    **f. Simpan konfigurasi yang sedang berjalan**  
        Untuk menghindari hilangnya konfigurasi yang diterapkan saat ini, perlu untuk menyimpannya di memori. Perangkat yang dimuat ulang (_reboot_) akan menghapus konfigurasi yang tidak disimpan di memori. Konfigurasi yang tersimpan akan dimuat oleh perangkat ketika perangkat memulai sistem.  
    ```
    R1#copy running-config startup-config
    ```
2. **Router 2 (R2)**  
    **a. Masuk ke mode terminal (CLI) perangkat.**  
    **b. Nonaktifkan _Domain Lookup_.**  
        Sama seperti langkah kedua konfigurasi ***Router 1*** sebelumnya yaitu menonaktifkan fitur _Domain Lookup_.  
    **- Masuk ke _Privileged Mode_ dari _User Mode_ dengan perintah berikut:**  
    ```
    Router>enable
    ```  
    **- Setelah masuk ke _Privileged Mode_, masuk ke _Global Configuration Mode_ dengan perintah berikut:**  
    ```
    Router#config terminal
    ```  
    **- Mengubah nama host (_hostname_) dengan perintah berikut:**  
    ```
    Router(config)#hostname R2
    ```  
    **- Nonaktifkan _domain-lookup_ dengan perintah berikut:**  
    ```
    R2(config)#no ip domain-lookup
    ```  
    **c. Konfigurasi alamat IP (IP _Address_) untuk _interface_ Gig0/0 seperti berikut:**  
    ```
    R2(config)#int gig0/0
    R2(config-if)#description Connection to R1
    R2(config-if)#ip address 10.10.10.2 255.255.255.248
    R2(config-if)#no shutdown
    R2(config-if)#exit
    ```
    **d. Konfigurasi alamat IP (_IP Address_) untuk _interface_ Gig0/1 seperti berikut:**  
    ```
    R2(config)#int gig0/1
    R2(config-if)#description Connection to PC1
    R2(config-if)#ip address 192.168.200.6 255.255.255.248
    R2(config-if)#no shutdown
    R2(config-if)#exit
    ```
    **e. Konfigurasi alamat perutean (_routing address_)**  
        Untuk melakukan konfigurasi perutean, dapat dilakukan dengan dua cara yaitu _next-hop_ IP _address_ dan _exit-interface_.  
    **- _Next-hop_ IP _Address_**  
    ```
    ip route <network-destination> <subnet-mask network-destination> <next-hop ip address>
    ```  
    ```
    R2(config)#ip route 192.168.100.0 255.255.255.248 10.10.10.1
    ```  
    **- _Exit-interface_**  
    ```
    ip route <network-destination> <subnet-mask network-destination> <exit-interface>  
    ```
    ```
    R2(config)#ip route 192.168.100.0 255.255.255.248 gig0/1
    ```  
    **f. Simpan konfigurasi yang berjalan saat ini**  
    ```
    R2#copy running-config startup-config
    ```
3. **Personal Computer (PC)**  
    - _IP Configuration_ PC1  

    ![IP Configuration PC1](/img/static-routing/IP-Configuration_PC1.png)
  
    ---

    - _IP Configuration_ PC2  

    ![IP Configuration PC2](/img/static-routing/IP-Configuration_PC2.png)

---

### Pengujian  

1. Periksa konfigurasi perutean dengan perintah `show ip route`  

    ![Konfigurasi Perutean R1](/img/static-routing/pengujian_show-ip-route_1.png)  

    ---  

    ![Konfigurasi Perutean R2](/img/static-routing/pengujian_show-ip-route_2.png)

2. Lakukan ***ping*** antar _router_ dengan perintah `ping`   

    ![Ping R1 to R2](/img/static-routing/ping_R1-to-R2.png)  

    ---

    ![Ping R2 ke R1](/img/static-routing/ping_R2-to-R1.png)

3. Lakukan ***ping*** antar _Personal Computer_ dengan aplikasi _Command Prompt_ yang tersedia di _Desktop_  

    ![Ping PC1 ke PC2](/img/static-routing/ping_PC1-to-PC2.png)  

    ---

    ![Ping PC2toPC1](/img/static-routing/ping_PC2-to-PC1.png)

4. Dengan ***simple Protocol Data Unit (PDU)***  

    ![Simple PDU](/img/static-routing/simple-PDU.png)  

> Catatan:  
 Pengujian konektivitas dengan ***simple PDU*** untuk kali pertama, umumnya akan mengalami kegagalan dan membutuhkan beberapa kali percobaan. Hal tersebut disebabkan karena proses ***Address Resolution Protocol (ARP)***. Saat perangkat mengirim paket, perangkat tersebut perlu mengetahui alamat MAC tujuan. Jika alamat MAC belum ada di _cache_ ARP-nya, perangkat akan mengirimkan permintaan ARP (_broadcast_) untuk menemukannya. Proses ini dapat menyebabkan penundaan, yang menyebabkan paket awal kehabisan waktu dan dianggap gagal. Paket-paket selanjutnya biasanya berhasil karena alamat MAC kemudian disimpan di _cache_ ARP. Jika proses pengujian dilakukan dengan _ping_, biasanya akan diawali dengan hasil ***Request timed out.***.  

 ---

## Penutup  

Setelah mempelajari konsep ***static routing*** dan langkah-langkah konfigurasinya melalui aplikasi simulasi ***Cisco Packet Tracer***, semoga tulisan ini dapat membantu teman-teman memahami jaringan komputer dengan lebih baik serta menumbuhkan semangat untuk terus belajar.  
Terima kasih telah meluangkan waktu untuk membaca. Penulis memohon maaf apabila terdapat kesalahan atau kekurangan dalam penyampaian materi. Sampai bertemu di tulisan berikutnya. Salam hangat!

---

## Lampiran

Jika ada dari teman-teman yang tertarik untuk mendapatkan file praktik ***.pkt*** (ekstensi ***Cisco Packet Tracer***) dari materi ini, dapat mengunduhnya pada tautan berikut:  
[static-routing.pkt](/files/static-routing/static-routing.pkt)

---

## Referensi  

1. Cisco Networking Academy: https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html  
2. RFC 1812 – Requirements for IP Version 4 Routers: https://www.rfc-editor.org/rfc/rfc1812  
3. Tanenbaum, A. S., & Wetherall, D. J. (2011). _Computer Networks (5th ed.)_. Pearson.
4. Taufik, M. (2015). _The Way to Be Cisco Warrior: Teori & Praktik_. Jakarta: ID-Networkers.
5. Setiawan, A. (2015). _CCNA Lab Guide (Edisi Pertama)_. Bandung: Nixtrain.