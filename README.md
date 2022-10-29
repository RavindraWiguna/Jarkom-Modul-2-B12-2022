# Jarkom-Modul-2-B12-2022

## Soal-Shift
![](.//images/Grafjarkom.png)

## Soal 1
WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden. Semua node terhubung pada router Ostania, sehingga dapat mengakses internet
### Cara Pengerjaan
1. Pertama tama, kita buat dahulu node-node yang dibutuhkan dan link-an mereka sesuai soal.<br>
2. Ubah network configuration setiap nodenya sesuai dengan bagaimana mereka dihubungkan. Berikut konfigurasi network tiap node<br>
* Ostania
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
    address 10.9.1.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 10.9.2.1
    netmask 255.255.255.0

auto eth3
iface eth3 inet static
    address 10.9.3.1
    netmask 255.255.255.0

```
* Wise
```
auto eth0
iface eth0 inet static
    address 10.9.3.2
    netmask 255.255.255.0
    gateway 10.9.3.1
```
* Berlint
```
auto eth0
iface eth0 inet static
    address 10.9.2.2
    netmask 255.255.255.0
    gateway 10.9.2.1
```
* Eden
```
auto eth0
iface eth0 inet static
    address 10.9.2.3
    netmask 255.255.255.0
    gateway 10.9.2.1
```
* SSS
```
auto eth0
iface eth0 inet static
    address 10.9.1.2
    netmask 255.255.255.0
    gateway 10.9.1.1
```
* Garden
```
auto eth0
iface eth0 inet static
      address 10.9.1.3
      netmask 255.255.255.0
      gateway 10.9.1.1
```
3. Setelah mengatur konfigurasi network tiap node, kita tambahkan IP DNS dari Ostania sebagai nameserver ke tiap node di dalam /etc/resolv.conf. Kita cek IP DNS yang berada di Ostania dengan memasukkan command `cat /etc/resolv.conf`. Terlihat isinya adalah `nameserver 192.168.122.1`, menandakan IP DNS-nya adalah `192.168.122.1`.
![](.//images/ipdns.png)
Lalu kita tambahkan juga `nameserver 192.168.122.1` ke dalam file `/etc/resolv.conf` di tiap node. Agar hal ini dilakukan otomatis setiap membuka project GNS3, kita masukkan command `echo nameserver 192.168.122.1 > /etc/resolv.conf` ke dalam file `.bashrc` di tiap nodenya (kecuali Ostania). Setelah itu kita masukkan command `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.9.0.0/16` pada Ostania.
4. Cek koneksi tiap nodenya dengan memping google.com.
### Kendala
Tidak ada

## Soal 2
Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise
### Cara Pengerjaan
A
### Kendala
Tidak ada

## Soal 3
Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden.
### Cara Pengerjaan
A
### Kendala
Tidak ada

## Soal 4
Buat juga reverse domain untuk domain utama
### Cara Pengerjaan
A
### Kendala
Tidak ada

## Soal 5
Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama
### Cara Pengerjaan
A
### Kendala
Tidak ada

## Soal 6
Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.yyy.com dengan alias www.operation.wise.yyy.com yang didelegasikan dari WISE ke Berlint dengan IP menuju ke Eden dalam folder operation.
### Cara Pengerjaan
A
### Kendala
Tidak ada

## Soal 7
Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden
### Cara Pengerjaan
A
### Kendala
Tidak ada
