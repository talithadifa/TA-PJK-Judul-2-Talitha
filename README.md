# Lab 10.4.4 - Build a Switch and Router Network
## Video Tugas Akhir PJK Judul 2
Berikut untuk link youtube dari penjelasan saya 
(https://youtu.be/uAjicS8uH7Q)


## Deskripsi Singkat

Pada lab ini, saya membuat simulasi jaringan sederhana menggunakan **Cisco Packet Tracer** dengan tujuan untuk menghubungkan dua jaringan berbeda melalui sebuah router. Topologi yang digunakan terdiri dari satu **router (R1)**, satu **switch (S1)**, dan dua **komputer (PC-A dan PC-B)**.

Seluruh perangkat saya konfigurasi agar bisa saling berkomunikasi antar-subnet, lalu diuji konektivitasnya menggunakan perintah **ping**.


## Tujuan
* Mengonfigurasi router, switch, dan dua PC agar bisa saling terhubung.
* Membangun dua subnet berbeda dan menghubungkannya lewat router.
* Memastikan konektivitas end-to-end berjalan lancar tanpa error.


### Tabel Alamat IP

| Perangkat | Interface | IP Address  | Subnet Mask   | Gateway     |
| --------- | --------- | ----------- | ------------- | ----------- |
| R1        | G0/0/0    | 192.168.0.1 | 255.255.255.0 | —           |
| R1        | G0/0/1    | 192.168.1.1 | 255.255.255.0 | —           |
| S1        | VLAN 1    | 192.168.1.2 | 255.255.255.0 | 192.168.1.1 |
| PC-A      | NIC       | 192.168.1.3 | 255.255.255.0 | 192.168.1.1 |
| PC-B      | NIC       | 192.168.0.3 | 255.255.255.0 | 192.168.0.1 |


## Langkah Konfigurasi

### 1. Router (R1)

Pertama saya masuk ke mode konfigurasi dan mengganti nama router menjadi **R1**.
Saya juga menonaktifkan DNS lookup agar tidak muncul delay jika salah ketik perintah.
Kemudian saya mengatur password untuk console dan VTY, mengenkripsi semua password, dan menambahkan banner peringatan login.

Selanjutnya, saya konfigurasi dua interface router:

* **G0/0/0** mengarah ke jaringan PC-B dengan IP **192.168.0.1**
* **G0/0/1** mengarah ke jaringan PC-A dan Switch dengan IP **192.168.1.1**

Setelah itu, saya aktifkan kedua interface dengan perintah `no shutdown` dan menyimpan konfigurasi menggunakan `copy running-config startup-config`.


### 2. Switch (S1)

Pada switch, saya juga ubah hostname menjadi **S1**, menonaktifkan DNS lookup, dan mengatur IP address pada **VLAN 1** yaitu **192.168.1.2/24**.
Saya juga menambahkan **default gateway 192.168.1.1** agar switch bisa berkomunikasi keluar subnet.
Terakhir, konfigurasi saya simpan menggunakan perintah yang sama.


### 3. PC-A dan PC-B

Saya mengatur alamat IP secara manual di masing-masing PC:

* **PC-A**: 192.168.1.3 / 255.255.255.0 / gateway 192.168.1.1
* **PC-B**: 192.168.0.3 / 255.255.255.0 / gateway 192.168.0.1

Dengan begitu, masing-masing PC sudah terhubung ke subnet yang berbeda tapi bisa saling berkomunikasi lewat router.

## Pengujian Konektivitas

### Ping dari PC-A ke PC-B (192.168.0.3)

Saya mencoba melakukan ping dari **PC-A** menuju **PC-B**.
Hasilnya menunjukkan **1 kali Request Timed Out** dan **3 kali Reply berhasil**, artinya koneksi sudah berjalan namun ada sedikit delay saat awal komunikasi.

<img width="854" height="399" alt="Screenshot 2025-11-05 194609" src="https://github.com/user-attachments/assets/b7122e7f-a1dc-45b9-9a7f-daf0089ce7bd" />


### Ping dari Switch (S1) ke PC-B (192.168.0.3)

Selanjutnya, saya melakukan ping dari **switch S1** ke alamat yang sama (**192.168.0.3**) untuk memastikan routing antar-subnet benar-benar berfungsi.
Hasilnya **semua reply berhasil (100% sukses)**.

<img width="606" height="128" alt="Screenshot 2025-11-05 194742" src="https://github.com/user-attachments/assets/2b3c0cf6-41cb-4429-9180-0dff2c210b67" />


## Analisis

Dari hasil uji di atas, bisa disimpulkan bahwa konfigurasi jaringan sudah berjalan dengan baik.
Koneksi antar-subnet dapat dilakukan melalui router, dan semua perangkat bisa saling berkomunikasi.

Jika masih ada *Request Timed Out* di awal ping, biasanya disebabkan oleh **ARP request pertama** yang membutuhkan waktu untuk menemukan alamat MAC tujuan, hal ini normal dan bukan tanda error konfigurasi.


## Refleksi

**1️. Jika interface G0/0/1 administratively down:**
Gunakan perintah:

`
R1(config-if)# no shutdown
`

Ini akan menyalakan interface dan mengaktifkan koneksi fisik ke switch.

**2. Jika IP di G0/0/1 salah dikonfigurasi (misalnya 192.168.1.2):**
Akan terjadi **konflik IP** dengan switch, dan PC-A tidak bisa menemukan gateway. Akibatnya, ping akan gagal (RTO).


## Kesimpulan

* Semua perangkat (R1, S1, PC-A, dan PC-B) sudah berhasil dikonfigurasi.
* Koneksi antar-subnet berjalan baik.
* Ping dari berbagai perangkat menunjukkan hasil **Reply sukses**.
* Router berfungsi sebagai penghubung utama antar jaringan.
