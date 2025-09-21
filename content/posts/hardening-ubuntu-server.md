+++
date = '2025-09-13T23:15:22+07:00'
draft = false
title = 'Hardening Ubuntu Server'
showToc = true
tocOpen = true
tocTitle = 'Daftar Isi'
+++

> Disclaimer!  
Tulisan ini memuat materi yang tidak sepenuhnya berasal dari penulis sendiri, melainkan dirangkum dari berbagai sumber tepercaya dan dibantu oleh kecerdasan buatan. Seluruh referensi yang digunakan telah dicantumkan di bagian akhir, sehingga pembaca dapat dengan mudah menelusuri dan mempelajari sumber aslinya.  

## Pengantar

Bismillah.  
Tulisan ini menjadi pembuka dari seri belajar _system administration_ sekaligus keamanan siber. Pada bagian pertama ini, kita akan membahas cara mengamankan _server_ melalui teknik hardening. Praktik konfigurasi akan dilakukan menggunakan sistem operasi ***Ubuntu Server 24.02 LTS***.  

---

## Pendahuluan

### Apa itu _Hardening Server_ ?

_Server hardening_ adalah proses mengamankan sistem _server_ dari berbagai potensi ancaman dengan mengurangi permukaan serangan (_attack surface_).  
Intinya: meminimalkan celah yang bisa dimanfaatkan penyerang melalui konfigurasi yang ketat, pembatasan layanan, dan penerapan praktik keamanan terbaik.

## Manfaat atau Kegunaan

- Mengurangi Risiko Serangan.  
    > Meminimalkan kemungkinan eksploitasi _bug_/kerentanan.
- Menjaga Kerahasiaan & Integritas Data.  
    > Data sensitif lebih terlindungi dari akses ilegal atau modifikasi.  
- Kepatuhan Regulasi.  
    > Banyak standar (misal ISO 27001, PCI DSS, HIPAA) mengharuskan pengamanan tingkat _server_.  
- Stabilitas & Keandalan.  
    > Layanan penting tetap berjalan meski ada upaya serangan.  
-  Menghemat Biaya Jangka Panjang.  
    > Biaya insiden keamanan jauh lebih besar daripada investasi _hardening_.

---

## Praktik

### Persiapan Alat dan Bahan

Alat dan bahan yang dibutuhkan dalam praktik belajar _hardening ubuntu server_ yaitu:
1. Alat:
    - Aplikasi Virtualisasi **Oracle VirtualBox**
    - Aplikasi Editor Teks (bisa menggunakan bawaan sistem operasi)
2. Bahan:
    - ISO _Ubuntu Server_ 24.02 LTS atau _Image_ Mesin Virtual _Ubuntu Server_ 24.02 LTS 

---

### Konfigurasi

Proses _hardening_ dilakukan secara bertahap untuk memudahkan dokumentasi jika diperlukan. Tahap-tahap _hardening_ pada praktik ini ada _best practice_ yang bisa disesuaikan dengan kebutuhan.  
**A. Persiapan Dasar**
1. **Update Sistem & Paket**
    ```
    sudo apt update && sudo apt upgrade -y
    sudo apt dist-upgrade -y
    sudo apt autoremove --purge
    ```
    > Pastikan ***kernel*** dan paket penting terbaru.  
2. **Gunakan Akun Non-Root + sudo**
    - Buat _user_ khusus admin:
    ```
    sudo adduser namauser
    sudo usermod -aG sudo namauser
    ```
    - Matikan _login root_ langsung via SSH.  
  
**B. Keamanan Akses**  
1. **Konfigurasi SSH**
    - Edit `/etc/ssh/sshd_config`:
    ```
    PermitRootLogin no
    PasswordAuthentication no
    AllowUsers namauser
    ```
    - Gunakan _key based authentication_
2. ***Port Knocking*** **atau Ganti ***Port*** (Opsional)**
    > Mengubah _port default_ (22) ke _port_ lain bukan solusi utama, tapi menurunkan _noise bot_  

**C. Firewall & Kontrol Jaringan**
1. ***UFW (Uncomplicated Firewall)***
    ```
    sudo ufw default deny incoming
    sudo ufw default allow outgoing
    sudo ufw allow OpenSSH
    sudo ufw enable
    ```
2. ***Fail2ban***  
    - Memblokir IP yang gagal _login_ berulang:
    `sudo apt install fail2ban`  
    - Atur `/etc/fail2ban/jail.local`  

**D. Proteksi Layanan & Paket**
1. **Minimalisasi _Service_**
    - Periksa _service_: `sudo systemctl list-unit-files --type=service`
    - Nonaktifkan yang tidak perlu: `sudo systemctl disable nama_service --now`  
2. **AppArmor/SELinux**
    - _Ubuntu default_: AppArmor aktifkan & profilkan sesuai aplikasi.  
3. **Audit & Monitoring**
    - Gunakan `auditd`, `logwatch`, atau SIEM seperti Wazuh untuk memantau.

***D. Backup & Recovery***
- Rutin _Backup off-site_ (misal `rclone`, `borg`, `restic`).
- Uji proses _restore_ agar tidak hanya "sekedar punya _backup_".

---

### Contoh _Script_ Otomatisasi _Hardening Ubuntu Server_ dengan Bash

```
#!/bin/bash

# Update system
apt update && apt upgrade -y

# Disable SSH Root Login
sed -i 's/#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config

# Disable Password authentication
sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config

# Restart SSH
systemctl restart sshd

# Install firewall and allow essentials ports
apt install -y ufw
ufw allow 22/tcp
ufw allow 443/tcp
ufw enable

# Remove unnecessary packages
apt purge -y telnet ftp nfs-common

# Install fail2ban
apt install -y fail2ban
systemctl enable fail2ban
systemctl start fail2ban

# Install and enable auditd
apt install -y auditd
systemctl enable auditd
systemctl start auditd

echo "[✔] Hardening selesai. Harap tinjau konfigurasi tambahan secara manual."
```

---

## Penutup

Memahami konsep hardening merupakan langkah penting dalam pengelolaan _server_. Melalui latihan dan praktik sederhana, keterampilan hardening menjadi bekal berharga bagi setiap _system administrator_.  
Terima kasih telah meluangkan waktu untuk membaca hingga akhir. Penulis memohon maaf apabila terdapat kekeliruan atau kekurangan dalam penyampaian materi. Sampai jumpa di tulisan berikutnya. Salam hangat!  

---

## Referensi

1. _Ubuntu Security Guide_: https://ubuntu.com/security/guide
2. _CIS Benchmark for Ubuntu Server 22.04 (CIS Workbench, 2024)_
3. _NIST Special Publication 800-123: Guide to General Server Security (Rev.2, 2023)_
4. _OWASP Server Security Cheat Sheet (2024 update)_