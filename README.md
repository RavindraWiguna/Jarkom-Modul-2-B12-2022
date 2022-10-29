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
3. Setelah mengatur konfigurasi network tiap node, kita tambahkan IP DNS dari Ostania sebagai nameserver ke tiap node di dalam /etc/resolv.conf. Kita cek IP DNS yang berada di Ostania dengan memasukkan command `cat /etc/resolv.conf`. Terlihat isinya adalah `nameserver 192.168.122.1`, menandakan IP DNS-nya adalah `192.168.122.1`.<br>
![](.//images/ipdns.png)<br>
Lalu kita tambahkan juga `nameserver 192.168.122.1` ke dalam file `/etc/resolv.conf` di tiap node. Agar hal ini dilakukan otomatis setiap membuka project GNS3, kita masukkan command `echo nameserver 192.168.122.1 > /etc/resolv.conf` ke dalam file `.bashrc` di tiap nodenya (kecuali Ostania). Setelah itu kita masukkan command `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.9.0.0/16` pada file `.bashrc` di Ostania.
4. Cek koneksi tiap nodenya dengan memping google.com.
<img src="https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no 1/picture1.png" style="width:86%;height:86%;"><br>
<img src="https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no 1/picture2.png" style="width:86%;height:86%;"><br>
<img src="https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no 1/picture3.png" style="width:86%;height:86%;"><br>
<img src="https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no 1/picture4.png" style="width:86%;height:86%;"><br>
<img src="https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no 1/picture5.png" style="width:86%;height:86%;"><br>
<img src="https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no 1/picture6.png" style="width:86%;height:86%;"><br>

### Kendala
Tidak ada

## Soal 2
Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise
### Cara Pengerjaan
1. Pertama tama kita install terlebih dahulu bind9 di WISE dengan command
```
apt-get update
apt-get install bind9 -y
```
2. Kemudian kita isikan konfigurasi domain wise.b12.com pada file `/etc/bind/named.conf.local` seperti berikut:
```
zone "wise.b12.com" {
        type master;
        file "/etc/bind/wise/wise.b12.com";
};
```
3. Lalu, buat folter wise pada directory `/etc/bind/wise` dengan command `mkdir /etc/bind/wise`.
4. Tambahkan file wise.b12.com pada folder wise tersebut yang isinya:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.b12.com. root.wise.b12.com. (
                        4               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      wise.b12.com.
@       IN      A       10.9.3.2
www     IN      CNAME   wise.b12.com.
```
5. Lalu kita restart bind9 dengan command `service bind9 restart`
6. Pada tiap client, kita tambahkan nameserver IP Wise ke dalam `etc/resolv.conf` menjadi seperti ini:
```
nameserver 10.9.3.2
nameserver 192.168.122.1
```
7.Lalu kita tes apakah sudah berhasil dengan memping wise.b12.com dan cek apakah www.wise.b12.com ialah alias dari wise.b12.com dengan command `host -t CNAME www.wise.b12.com`
<img src="https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no 2/picture1.png" style="width:86%;height:86%;"><br>
<img src="https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no 2/picture2.png" style="width:86%;height:86%;"><br>
### Kendala
Tidak ada

## Soal 3
Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden.
### Cara Pengerjaan
1. Kita tambahkan isi file dari `/etc/bind/wise/wise.b12.com` menjadi seperti berikut
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.b12.com. root.wise.b12.com. (
                        4               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      wise.b12.com.
@               IN      A       10.9.3.2
www             IN      CNAME   wise.b12.com. 
eden            IN      A       10.9.2.3
www.eden        IN      CNAME   eden.wise.b12.com.
```
2. Restart bind9 dengan command `service bind9 restart`
3. Cek pada client apakah berhasil dengan memping eden.wise.b12.com dan mengecek apakah www.eden.wise.b12.com adalah alias dari eden.wise.b12.com dengan command `host -t CNAME www.eden.wise.b12.com`
<img src="https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no 3/picture1.png" style="width:86%;height:86%;"><br>
### Kendala
Tidak ada

## Soal 4
Buat juga reverse domain untuk domain utama
### Cara Pengerjaan
1. Pada file `/etc/bind/named.conf.local` kita tambahkan konfigurasi untuk reverse domainnya
```
zone "wise.b12.com" {
        type master;
        file "/etc/bind/wise/wise.b12.com";
};

zone "3.9.10.in-addr.arpa" {
    type master;
    file "/etc/bind/wise/3.9.10.in-addr.arpa";
};
```
2. Tambahkan file baru ke directory `/etc/bind/wise/` dengan nama `3.9.10.in-addr.arpa` yang isinya adalah sebagai berikut:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.b12.com. root.wise.b12.com. (
                        2               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
3.9.10.in-addr.arpa.    IN      NS      wise.b12.com.
2                       IN      PTR     wise.b12.com.
```
3. Restart bind9 dengan command `service bind9 restart`.
4. Pada client, install dnsutils dengan command:
```
apt-get update
apt-get install dnsutils -y
```
5. Cek apakah 2.3.9.10.in-addr-arpa sudah benar menuju wise.b12.com dengan command `host -t PTR 10.9.3.2`
<img src="https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no 4/picture1.png" style="width:86%;height:86%;"><br>
### Kendala
Tidak ada

## Soal 5
Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama
```
zone "wise.a03.com" {
        type master;
        file "/etc/bind/wise/wise.b12.com";
        allow-transfer { 10.9.3.2; };
};

```
- Lalu kita restart service bind9 dengan command ``` service bind9 restart ```
- Setelah itu, kita akan menginstall bind9 juga pada node **Berlint** dengan command 

```
apt-get update
apt-get install bind9 -y
```
- Pada file **named.conf.local** di **Berlint** kita isi konfigurasi berikut
```
zone "wise.b12.com" {
    type slave;
    masters { 10.9.1.2; }; // Masukan IP EniesLobby tanpa tanda petik
    file "/var/lib/bind/wise.b12.com";
};
```
- Restart service bind9 dengan command ``` service bind9 restart ```
- Setelah semuanya telah dilakukan maka untuk testing kita akan melakukan ping kepada **wise.b12.com** (matikan terlebih dahulu service bind9 pada **WISE** dengan command ``` service bind9 stop ```
### Kendala
Tidak ada

## Soal 6
Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.yyy.com dengan alias www.operation.wise.yyy.com yang didelegasikan dari WISE ke Berlint dengan IP menuju ke Eden dalam folder operation.
### Cara Pengerjaan
- Untuk mendelegasikan domain atau subdomain kita akan menambah konfigurasi pada file **wise.a03.com** di **WISE** seperti berikut
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.b12.com. root.wise.b12.com. (
                     2022100601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      wise.b12.com.
@               IN      A       10.9.1.2
www             IN      CNAME   wise.b12.com.
eden            IN      A       110.9.3.3
www.eden        IN      CNAME   eden.wise.b12.com.
ns1             IN      A       10.9.3.2
operation       IN      NS      ns1
@               IN      AAAA    ::1
```
- Kemudian pada file **named.conf.options** di **WISE** kita akan melakukan uncomment pada **dnssec-validation auto;** dan tambahkan ``` allow-query{any;}; ``` dibawahnya.
- Setelah itu, di node **Berlint** pada file **named.conf.options** kita juga akan melakukan uncomment pada **dnssec-validation auto;** dan tambahkan ``` allow-query{any;}; ``` dibawahnya.
- Untuk file **named.conf.local** pada node **Berlint** kita tambahkan konfigurasi seperti berikut
```
zone "operation.wise.b12.com" {
        type master;
        file "/etc/bind/delegasi/operation.wise.b12.com";
};
```
- Buat direktori dengan nama **/etc/bind/delegasi** dan copy file **db.local** ke dalam direktori **/etc/bind/delegasi** dengan nama **operation.wise.b12.com** dengan command berikut
```
mkdir delegasi
cp /etc/bind/db.local /etc/bind/delegasi/operation.wise.b12.com
```
- Isi konfigurasi untuk file **operation.wise.b12.com** seperti dibawah ini
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     operation.wise.a03.com. root.operation.wise.b12.com. (                  
		     2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      operation.wise.b12.com.
@       IN      A       10.9.3.2
www     IN      CNAME   operation.wise.b12.com.
@       IN      AAAA    ::1
```
- Restart service bind9 dengan menggunakan command ``` service bind9 restart ```
### Kendala
Tidak ada

## Soal 7
Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden
### Cara Pengerjaan
- Untuk menambah subdomain, maka kita menggunakan DNS Record tipe A dengan menambahkan nama subdomain pada file **operation.wise.b12.com** pada node **Berlint** sekaligus dengan alias dengan menggunakan DNS Record tipe CNAME seperti dibawah ini
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     operation.wise.b12.com. root.operation.wise.b12.com. (                  
			   2022102601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      operation.wise.b12.com.
@       IN      A       10.9.3.2
www     IN      CNAME   operation.wise.b12.com.
strix   IN      A       110.9.3.3
www.strix       IN      CNAME   strix.operation.wise.b12.com.
@       IN      AAAA    ::1
```
- Restart service bind9 dengan command ``` service bind9 restart ```
### Kendala
Tidak ada

### 8
> Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver **www.wise.yyy.com**. Pertama, Loid membutuhkan webserver dengan DocumentRoot pada /var/www/wise.yyy.com.

### Penyelesaian
***Pada node SSS dan Garden***

Lakukan `apt-get update` dan juga penginstalan lynx dengan cara 

```
apt-get update
apt-get install lynx -y
```

***Pada node Eden***

Lakukan instalasi Apache, php, openssl serta git,unzip nya untuk melakukan download ke website https dengan cara

```
apt-get install apache2 -y
apt-get install php -y
apt-get install libapache2-mod-php7.0 -y
apt-get install ca-certificates openssl -y
apt-get install unzip -y
apt-get install git -y
```

Untuk mendownload file, lakukan `git clone https://github.com/Tioardhana/modul2source-jarkom.git` (repository tambahan khusus file zip yang dibutuhkan) dan unzip file nya dengan command `unzip -o /root/modul2source-jarkom/\*.zip -d /root/modul2source-jarkom`.

Copy file `default.conf` ke `wise.b12.com.conf` dengan perintah `cp soal8/default.conf /etc/apache2/sites-available/wise.b12.com.conf`. 
  
Kemudian aktifkan website dengan `a2ensite wise.b12.com`, lalu buat direktori `var/www/wise.b12.com` dengan perintah `mkdir /var/www/wise.b12.com` dan copy file dengan perintah `cp soal8/wise.b12.com/index.php /var/www/wise.b12.com`.
  
Restart apache dengan `service apache2 restart`.

Lakukan tes pada node SSS dan Garden dengan `lynx http://www.wise.b12.com` atau `lynx http://www.wise.b12.com/index.php` maka akan mendapatkan tampilan seperti berikut 

![image](https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no%208/picture1.png)

### 9
> Setelah itu, Loid juga membutuhkan agar url **www.wise.yyy.com/index.php/home** dapat menjadi menjadi **www.wise.yyy.com/home**.

### Penyelesaian
Pada node Eden, copy semua file di folder wise yang sudah diunzip dengan command `cp -r /root/modul2source-jarkom/wise/. /var/www/wise.b12.com`, selanjutnya konfigurasi file `wise.b12.com.conf` dengan `cp soal9/default.conf /etc/apache2/sites-available/wise.b12.com.conf` dengan isi `default.conf` sebagai berikut.

```
<VirtualHost *:80>
        ServerName wise.b12.com
        ServerAlias www.wise.b12.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.b12.com

        Alias "/home" "/var/www/wise.b12.com/index.php/home"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Lakukan restart apache2 dengan `service apache2 restart`
  
Terakhir lakukan testing pada node Garden dan SSS dengan command `lynx www.wise.b12.com/home`

![image](https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no%209/picture1.png)

### 10
> Setelah itu, pada subdomain **www.eden.wise.yyy.com**, Loid membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/eden.wise.yyy.com.

### Penyelesaian
***Pada node Eden***

Lakukan konfigurasi pada `default.conf` dengan 

```
<VirtualHost *:80>
        ServerName eden.wise.b12.com
        ServerAlias www.eden.wise.b12.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.b12.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Kemudian copy file dengan perintah `cp soal10/default.conf /etc/apache2/sites-available/eden.wise.b12.com.conf`.

Lalu, aktifkan virtualhost dengan a2ensite, membuat direktori untuk documentroot di `/var/www/eden.wise.b12.com` dan jangan lupa untuk melakukan copy content ke documentroot dengan cara

```
a2ensite eden.wise.b12.com

mkdir /var/www/eden.wise.b12.com
cp -r /root/modul2source-jarkom/eden.wise/. /var/www/eden.wise.b12.com
```

Restart apache `service apache2 restart`.

Lakukan testing pada node Garden dan SSS dengan command `lynx http://www.eden.wise.b12.com`.

![image](https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no%2010/picture1.png)

### 11
> Akan tetapi, pada folder /public, Loid ingin hanya dapat melakukan directory listing saja.

### Penyelesaian
***Pada node Eden***

Konfigurasi file `default.conf` dengan 

```
<VirtualHost *:80>
        ServerName eden.wise.b12.com
        ServerAlias www.eden.wise.b12.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.b12.com

        <Directory /var/www/eden.wise.b12.com>
                Options +Indexes
        </Directory>

        <Directory /var/www/eden.wise.b12.com/error>
                Options -Indexes
        </Directory>

        <Directory /var/www/eden.wise.b10.com/public>
                Options +Indexes
        </Directory>
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

dan jangan lupa copy file dengan command `cp soal11/default.conf /etc/apache2/sites-available/eden.wise.b12.com.conf`.

Kemudian restart apache `service apache2 restart`.

Lalu, lakukan testing dengan command `lynx http://www.eden.wise.b12.com/public` maka akan mendapat tampilan seperti berikut.

![image](https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no%2011/picture1.png)

### 12
> Tidak hanya itu, Loid juga ingin menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache.

### Penyelesaian
***Pada node Eden***

Konfigurasi file `default.conf` dengan menambhkan `ErrorDocument 404 /error/404.html` sehingga terdapat pesan error yang dibuat sendiri seperti berikut

```
<VirtualHost *:80>
        ServerName eden.wise.b12.com
        ServerAlias www.eden.wise.b12.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.b12.com

        <Directory /var/www/eden.wise.b12.com>
                Options +Indexes
        </Directory>

        <Directory /var/www/eden.wise.b12.com/public>
                Options +Indexes
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        ErrorDocument 404 /error/404.html
  </VirtualHost>
```
  
Copy file dengan command `cp soal12/default.conf /etc/apache2/sites-available/eden.wise.b12.com.conf`.

Restart apache `service apache2 restart`.

Lakukan testing pada node Garden dan SSS ketika mengakses url invalid seperti `lynx http://www.eden.wise.b12.com/wrong`, maka akan mendapatkan tampilan berikut.

![image](https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no%2012/picture1.png)

### 13
> Loid juga meminta Franky untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset **www.eden.wise.yyy.com/public/js** menjadi **www.eden.wise.yyy.com/js**.

### Penyelesaian
***Pada node Eden***

Lakukan konfigurasi pada file `default.conf` dengan menambahkan `Alias "/js" "/var/www/eden.wise.b12.com/public/js"` sebagi berikut.

```
<VirtualHost *:80>
        ServerName eden.wise.b12.com
        ServerAlias www.eden.wise.b12.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.b12.com

        <Directory /var/www/eden.wise.b12.com>
                Options +Indexes
        </Directory>

        <Directory /var/www/eden.wise.b12.com/public>
                Options +Indexes
        </Directory>

        Alias "/js" "/var/www/eden.wise.b12.com/public/js"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        ErrorDocument 404 /error/404.html
</VirtualHost>
```

Copy file dengan command `cp soal13/default6.conf /etc/apache2/sites-available/eden.wise.b12.com.conf`. 

Restart apache `service apache2 restrat`.

Lakukan testing pada node Garden dan SSS ketika mengakses `lynx http://www.eden.wise.b12.com/js`, maka akan mendapatkan tampilan seperti berikut.

![image](https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no%2013/picture1.png)

### 14
> Loid meminta agar **www.strix.operation.wise.yyy.com** hanya bisa diakses dengan port 15000 dan port 15500.

### Penyelesaian
***Pada node Eden***

Membuat konfigurasi Web Server di `default-15000.conf` dan `default-15500.conf` sebaagi berikut.

- default-wise-1-15000.conf

  ```
  <VirtualHost *:15000>
          ServerName strix.operation.wise.b12.com
          ServerAlias www.strix.operation.wise.b10.com

          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/strix.operation.wise.b12.com

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
  ```

- default-wise-1-15500.conf

  ```
  <VirtualHost *:15500>
          ServerName strix.operation.wise.b12.com
          ServerAlias www.strix.operation.wise.b10.com

          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/strix.operation.wise.b12.com

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
  ```

Kemudian copy file dengan command `cp soal14/default-15000.conf /etc/apache2/sites-available/strix.operation.wise.b10.com-15000.conf` dan `cp soal14/default-15500.conf /etc/apache2/sites-available/strix.operation.wise.b12.com-15000.conf`

Tambahkan port yang akan di listen pada `ports.conf` sebagi berikut.

```
Listen 80
Listen 15000
Listen 15500

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>
```

Kemudian copi file `cp /root/ports.conf /etc/apache2/ports.conf`.

Kemudian aktifkan a2ensite dengan `a2ensite strix.operation.wise.b12.com-15000` dan `a2ensite strix.operation.wise.b12.com-15500`.

Lakukan pembuatan direktori baru dengan `mkdir /var/www/strix.operation.wise.b12.com`.

Copy file - file lampiran github ke folder yang telah dibuat `cp -r /root/modul2source-jarkom/strix.operation.wise/. /var/www/strix.operation.wise.b12.com`.

Restart apache `service apache2 restart`.

Pada node Garden dan SSS, kita dapat melakukan testing dengan menggunakan lynx pada port 15000 atau 15500 yaitu `lynx http://www.strix.operation.wise.b10.com:15000` atau `lynx http://www.strix.operation.wise.b12.com:15500`.

![image](https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no%2014/picture1.png)

![image](https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no%2014/picture2.png)

### 15
> dengan autentikasi username Twilight dan password opStrix dan file di /var/www/strix.operation.wise.yyy

### Penyelesaian
***Pada node Eden*** 

Tambahkan code baru berikut pada file `default-15000.conf` dan `default-15500.conf` sebagai berikut.

- default-15000.conf

  ```
  <VirtualHost *:15000>
          ServerName strix.operation.wise.b12.com
          ServerAlias www.strix.operation.wise.b12.com

          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/strix.operation.wise.b12.com

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined

          <Directory "var/www/strix.operation.wise.b12.com">
                  AuthType Basic
                  AuthName "Restricted Content"
                  AuthUserFile /etc/apache2/.htpasswd
                  Require valid-user
          </Directory>
  </VirtualHost>
  ```

- default-15500.conf

  ```
  <VirtualHost *:15500>
          ServerName strix.operation.wise.b12.com
          ServerAlias www.strix.operation.wise.b12.com

          ServerAdmin webmaster@localhost
          DocumentRoot /var/www/strix.operation.wise.b12.com

          ErrorLog ${APACHE_LOG_DIR}/error.log
          CustomLog ${APACHE_LOG_DIR}/access.log combined

          <Directory "var/www/strix.operation.wise.b12.com">
                  AuthType Basic
                  AuthName "Restricted Content"
                  AuthUserFile /etc/apache2/.htpasswd
                  Require valid-user
          </Directory>
  </VirtualHost>
  ```

Copy file dengan command `cp soal15/default--15000.conf /etc/apache2/sites-available/strix.operation.wise.b12.com-15000` dan `cp soal15/default-15500.conf /etc/apache2/sites-available/strix.operation.wise.b12.com-15500`.

Kemudian buat autentikasi baru dengan command berikut sehingga memunculkan file .htpasswd pada dengan command `htpasswd -b -c /etc/apache2/.htpasswd Twilight opStrix`.

Restart apache `service apache2 restart`.

Ketika web server `strix.operation.wise.b12.com` diakses, akan diminta authentikasi username dan password.

Kemudian akan menampilakn hasil berikut.

![image](https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no%2015/picture1.png)

![image](https://github.com/RavindraWiguna/Jarkom-Modul-2-B12-2022/blob/main/images/no%2015/picture2.png)
