# Lapres Jarkom Modul 2 Kelompok D05 #

| Nama Praktikan  | NRP Praktikan |
| ------------- | ------------- |
| Ichlasul Hasanat  | 5025201091  |
| Haidar Fico Ramadhan Aryputra | 5025201185  |
| Naufal Ariq Putra Yosyam | 5025201112 |

## 1
#### WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden. Semua node terhubung pada router Ostania, sehingga dapat mengakses internet . ####
### Penyelesaian ###

Pertama Kita buka .bashrc di semua node lalu masukkan code seperti di bawah ini: 
### Ostania
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.187.0.0/16
```
### WISE DAN YANG LAIN-LAIN
```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
```

Lalu lakukan tes koneksi dengan ```ping google.com -c 3```

Maka hasilnya seperti ini :
![1.1](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar1.1.png)
![1.2](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar1.2.png)
![1.3](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar1.3.png)
![1.4](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar1.4.png)
![1.5](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar1.5.png)
![1.6](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar1.6.png)



## 2
#### Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise. ####

### Penyelesain ###

Pertama kita membuat file named.conf.local dan wise.d05.com pada root node WISE dengan isi sebagai berikut:
#### named.conf.local ####
```
zone "wise.d05.com" {
        type master;
        file "/etc/bind/wise/wise.d05.com";
};
```

#### wise.d05.com ####
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.d05.com. root.wise.d05.com. (
                        4               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      wise.d05.com.
@       IN      A       192.187.3.2
www     IN      CNAME   wise.d05.com.
```
Lalu kemudian kita buat script ```soal2.sh``` yang isinya sebagai berikut :
```
 apt-get update
 apt-get install bind9 -y

cp soal2/named.conf.local /etc/bind/named.conf.local

mkdir /etc/bind/wise

cp soal2/wise.d05.com /etc/bind/wise/wise.d05.com
```

Lalu di client SSS daan Garden tambahkan nameserver di file resolv.conf lalu  lakukan tes dengan script dibawah ini :
### resolv.conf ###
```
nameserver 192.187.3.2 // ip wise
nameserver 192.168.122.1 // ip dns
```
### soal2.sh ###
```
cp soal2/resolv.conf /etc/resolv.conf

ping wise.d05.com -c 5
host -t CNAME www.wise.d05.com
```

Hasilnya seperti yang dibawah ini :
![2.1](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar2.1.png)
![2.2](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar2.2.png)

## 3
#### Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden. ####
### Penyelesaian ###

Pertama kita buat folder soal3 yang isinya file wise.d05.com di WISE dengan isi sebagai berikut :
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.d05.com. root.wise.d05.com. (
                        4               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      wise.d05.com.
@       IN      A       192.187.3.2
www     IN      CNAME   wise.d05.com.
eden    IN      A       192.187.2.3
www.eden IN     CNAME   eden.wise.d05.com
```

Lalu kita buat script ``` soal3.sh ``` di WISE untuk membuat subdomain seperti berikut:
```
cp soal3/wise.d05.com /etc/bind/wise/wise.d05.com
service bind9 restart
```

Lakukan tes ping di SSS dan Garden Maka akan didapatkan hasil sebagai berikut :
```
ping eden.wise.d05.com -c 3
host -t CNAME www.eden.wise.d05.com
```

![3.1](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar3.1.png)
![3.2](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar3.2.png)



## 4
#### Buat juga reverse domain untuk domain utama. ####
### Penyelesaian ###
Buat 2 file di dalam folder soal4 pada node WISE yang isinya sebagai berikut :
### named.conf.local ###
```
zone "wise.d05.com" {
        type master;
        file "/etc/bind/wise/wise.d05.com";
};

zone "3.187.192.in-addr.arpa" {
    type master;
    file "/etc/bind/wise/3.187.192.in-addr.arpa";
};
```

### 3.187.192.in-addr.arpa ###
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.d05.com. root.wise.d05.com. (
                        2               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
3.187.192.in-addr.arpa. IN      NS      wise.d05.com.
2                       IN      PTR     wise.d05.com.
```

Lalu kita buat script ``` soal4.sh``` yang isinya sebagai berikut :
```
cp soal4/named.conf.local /etc/bind/named.conf.local
cp soal4/3.187.192.in-addr.arpa /etc/bind/wise/3.187.192.in-addr.arpa
service bind9 restart
```

Lalu lakukan tes di Client SSS dan Garden sebagai berikut :
```
apt-get update
apt-get install dnsutils


host -t PTR 192.187.3.2
```

![4.1](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar4.1.png)
![4.1](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar4.1.png)


## 5
#### Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama. ####
### Penyelesaian ###

Pertama kita buat folder Soal5 yang isinya file named.conf.local pada WISE dengan isi sebagai berikut :
```
zone "wise.d05.com" {
    type master;
    notify yes;
    also-notify { 192.187.2.2; };
    allow-transfer { 192.187.2.2; }; // Masukan IP berlint
    file "/etc/bind/wise/wise.d05.com";
};

zone "3.187.192.in-addr.arpa"{
        type master;
        file "/etc/bind/wise/3.187.192.in-addr.arpa";

}
```

Lalu kita buat script ``` soal5.sh ``` untuk menambahkan konfigurasi Berlint dengan isi sebagai berikut :
```
cp soal5/named.conf.local /etc/bind/named.conf.local

service bind9 restart
```

Kita buat folder soal5 yang isinya file named.conf.local pada Berlint yang isinya sebagai berikut :
```
zone "wise.d05.com" {
    type slave;
    masters { 192.187.3.2; }; // Masukan IP EniesLobby tanpa tanda petik
    file "/var/lib/bind/wise.d05.com";
};
```

Lalu buat script ``` soal5.sh ``` di Berlint sebagai berikut :
```
apt-get update
apt-get install bind9 -y

cp soal5/named.conf.local /etc/bind/named.conf.local
service bind9 restart
```

Lalu kita tes dengan pertama menjalankan file ``` tes.sh ``` yang isinya ``` service bind9 stop ``` Kemudian coba ping di Client SSS dan Garden sebagai berikut :
```
cp soal5/resolv.conf /etc/resolv.conf
ping wise.d05.com -c 3
```

![5.1](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar5.1.png)
![5.2](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar5.2.png)



## 6
#### Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.yyy.com dengan alias www.operation.wise.yyy.com yang didelegasikan dari WISE ke Berlint dengan IP menuju ke Eden dalam folder operation. ####

### Penyelesaian ###
Di WISE buat folder soal6 yang isinya file named.conf.options dan wise.d05.com yang isinya sebagai berikut :
### named.conf.options ###
```
options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable 
        // nameservers, you probably want to use them as forwarders.  
        // Uncomment the following block, and insert the addresses replacing 
        // the all-0's placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        // dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```
### wise.d05.com ###
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.d05.com. root.wise.d05.com. (
                        4               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      wise.d05.com.
@       IN      A       192.187.3.2
www     IN      CNAME   wise.d05.com. 
eden    IN      A       192.187.2.3
www.eden IN     CNAME   eden.wise.d05.com.
ns1     IN      A       192.187.2.2 
operation IN    NS      ns1 
```

Lalu buat script ``` soal6.sh ``` untuk menambahkan delegasi subdomain berupa operation serta meng-allow semua query dengan isian sebagai berikut :
```
cp soal6/named.conf.options /etc/bind/named.conf.options
cp soal6/wise.d05.com /etc/bind/wise/wise.d05.com

service bind9 restart
```

Lalu di Berlint kita buat folder soal6 yang isinya named.conf.local, named.conf.options, operation.wise.d05.com dengan isi sebagai berikut :
### named.conf.options ###
```
options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable 
        // nameservers, you probably want to use them as forwarders.  
        // Uncomment the following block, and insert the addresses replacing 
        // the all-0's placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        // dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```

### named.conf.local ###
```
zone "operation.wise.d05.com" {
    type master;
    file "/etc/bind/operation/operation.wise.d05.com";
};
```

### operation.wise.d05.com
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     operation.wise.d05.com. root.operation.wise.d05.com. (
                        4               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      operation.wise.d05.com.
@       IN      A       192.187.2.3
www     IN      CNAME   operation.wise.d05.com.
```

Lalu buat script ``` soal6.sh ``` yang isinya sebagai berikut :
```
cp soal6/named.conf.local /etc/bind/named.conf.local

mkdir /etc/bind/operation

cp soal6/operation.wise.d05.com /etc/bind/operation/operation.wise.d05.com

cp soal6/named.conf.options /etc/bind/named.conf.options

service bind9 restart
```
Lakukan tes ping di SSS dan Garden sebagai berikut :
```
ping operation.wise.d05.com -c 3
ping www.operation.wise.d05.com -c 3
```

![6.1](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar6.1.png)
![6.2](https://github.com/Naufalar10/Jarkom-Modul-2-D05-2022/blob/main/jarkom_2/gambar6.2.png)


## 7
Pertama, kita membuat file operation-2wise.d05.com pada node Berlint dengan isi sebagai berikut.
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     operation.wise.d05.com. root.operation.wise.d05.com. (
                        2022100601      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      operation.wise.d05.com.
@       IN      A       10.8.2.3
www     IN      CNAME   operation.wise.d05.com.
strix   IN      A       10.8.2.3
www.strix IN    CNAME   strix.operation.wise.d05.com.
```
Setelah itu kita membuat script untuk mencopy file tersebut ke dalam directory yang benar:
```
cp /root/operation-2.wise.d05.com /etc/bind/operation/operation.wise.d05.com

service bind9 restart
```
Lalu, kita lakukan tes pada client SSS melalui ping strix.operation.wise.d05.com -c 3 dan ping www.strix.operation.wise.d05.com -c 3.
## 8
Pertama kita install lynx dengan apt pada SSS dan garden. Lalu, Apache, php, openssl, git, dan unzip pada eden. Lalu kita clone repo yang dibutuhkan dengan `git clone https://github.com/Naufalar/modul2source-jarkom.git`. Lalu kita unzip dengan command `unzip -o /root/modul2source-jarkom/\*.zip -d /root/modul2source-jarkom`.

Lalu kita akan menyalin file default-wise-1.conf ke wise.d05.com.conf dengan perintah cp /root/default-wise-1.conf /etc/apache2/sites-available/wise.d05.com.conf.

Kemudian aktifkan website dengan a2ensite wise.d05.com, lalu buat direktori var/www/wise.d05.com dengan perintah mkdir /var/www/wise.d05.com dan copy file dengan perintah cp /root/wise.d05.com/index.php /var/www/wise.d05.com. Lalu restart server apache

Dapat dites dengan  node SSS dengan lynx http://www.wise.d05.com atau lynx http://www.wise.d05.com/index.php:
<img width="420" alt="Screen Shot 2022-10-29 at 22 12 49" src="https://user-images.githubusercontent.com/7030663/198839178-7f2f4dcb-ef4b-4578-8213-0572aa4943a6.png">


## 9
Pada node Eden, copy semua file di folder wise yang sudah diunzip `cp -r /root/modul2source-jarkom/wise/. /var/www/wise.d05.com`, lalu copy konfigurasi file wise.d05.com.conf `cp /root/default-wise-2.conf /etc/apache2/sites-available/wise.d05.com.conf` isi default-wise-2.conf sebagai berikut:
```
<VirtualHost *:80>
        ServerName wise.d05.com
        ServerAlias www.wise.d05.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.d05.com

        Alias "/home" "/var/www/wise.d05.com/index.php/home"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Restart apache lalu dengan lynx open website dalam Garden
![image](https://user-images.githubusercontent.com/7030663/198838999-7465aea4-5f1a-4a9c-b341-426c5b0095c1.png)

## 10
Pada node eden konfigurasi default-wise-3.conf berisi:
``` 
<VirtualHost *:80>
        ServerName eden.wise.d05.com
        ServerAlias www.eden.wise.d05.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.d05.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Lalu copy ke dalam apache dengan ```cp /root/default-wise-3.conf /etc/apache2/sites-available/eden.wise.d05.com.conf`
Lalu, aktifkan a2ensite, membuat direktori untuk documentroot di /var/www/eden.wise.d05.com lalu salin content ke documentroot dengan cara 
```
a2ensite eden.wise.d05.com

mkdir /var/www/eden.wise.d05.com
cp -r /root/modul2source-jarkom/eden.wise/. /var/www/eden.wise.d05.com
```
Restart apache lalu bisa di lynx dengan SSS

## 11
file default-wise-4.conf diisi dengan
```
<VirtualHost *:80>
        ServerName eden.wise.d05.com
        ServerAlias www.eden.wise.d05.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.d05.com

        <Directory /var/www/eden.wise.d05.com>
                Options +Indexes
        </Directory>

        <Directory /var/www/eden.wise.d05.com/error>
                Options -Indexes
        </Directory>

        <Directory /var/www/eden.wise.d05.com/public>
                Options +Indexes
        </Directory>
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Copy ke dalam /etc/apache2/sites-available/eden.wise.d05.com.conf. lalu buka dengan lynx

## 12
File default-wise-5.conf dengan menambhkan ErrorDocument 404 /error/404.html dengan isi:
```
<VirtualHost *:80>
        ServerName eden.wise.d05.com
        ServerAlias www.eden.wise.d05.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.d05.com

        <Directory /var/www/eden.wise.d05.com>
                Options +Indexes
        </Directory>

        <Directory /var/www/eden.wise.d05.com/public>
                Options +Indexes
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        ErrorDocument 404 /error/404.html
  </VirtualHost>
  ```
Copy file `cp /root/default-wise-5.conf /etc/apache2/sites-available/eden.wise.d05.com.conf.`

Restart apache

Lakukan testing pada node SSS ketika mengakses url invalid seperti lynx http://www.eden.wise.d05.com/abc
![image](https://user-images.githubusercontent.com/7030663/198839055-803bb335-4625-43ec-9fb2-2104f1cc5a7e.png)

## Soal 13

#### Loid juga meminta Franky untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.eden.wise.yyy.com/public/js menjadi www.eden.wise.yyy.com/js.

Lakukan konfigurasi pada file `default-wise-6.conf` di dalam node 'Eden' dengan menambahkan Alias `/js` `/var/www/eden.wise.d05.com/public/js` sebagai berikut.
```bash
<VirtualHost *:80>
        ServerName eden.wise.d05.com
        ServerAlias www.eden.wise.d05.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.d05.com

        <Directory /var/www/eden.wise.d05.com>
                Options +Indexes
        </Directory>

        <Directory /var/www/eden.wise.d05.com/public>
                Options +Indexes
        </Directory>

        Alias "/js" "/var/www/eden.wise.d05.com/public/js"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        ErrorDocument 404 /error/404.html
</VirtualHost>
```
Copy file dengan command cp `/root/default-wise-6.conf /etc/apache2/sites-available/eden.wise.d05.com.conf` lalu restart apache dengan `service apache2 restrat`.

Lakukan testing pada node client (Garden dan SSS) ketika mengakses `lynx http://www.eden.wise.d05.com/js`.

## Soal 14

#### Loid meminta agar www.strix.operation.wise.yyy.com hanya bisa diakses dengan port 15000 dan port 15500.

Membuat konfigurasi Web Server di `default-wise-1-15000.conf` dan `default-wise-1-15500.conf` pada node 'Eden' seperti berikut.

##### Dalam file `default-wise-1-15000.conf`
```bash
<VirtualHost *:15000>
        ServerName strix.operation.wise.d05.com
        ServerAlias www.strix.operation.wise.d05.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/strix.operation.wise.d05.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

##### Dalam file `default-wise-1-15000.conf`
```bash
<VirtualHost *:15500>
        ServerName strix.operation.wise.d05.com
        ServerAlias www.strix.operation.wise.d05.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/strix.operation.wise.d05.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Kemudian copy file dengan command `cp /root/default-wise-1-15000.conf /etc/apache2/sites-available/strix.operation.wise.d05.com-15000.conf` dan `cp /root/default-wise-1-15500.conf /etc/apache2/sites-available/strix.operation.wise.d05.com-15000.conf`.

Tambahkan port yang akan di listen pada `ports-1.conf` sebagai berikut.

```bash
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

Kemudian copy file ke dalam folder apache2 `cp /root/ports-1.conf /etc/apache2/ports.conf` lalu aktifkan `a2ensite` dengan `a2ensite strix.operation.wise.d05.com-15000` dan `a2ensite strix.operation.wise.d05.com-15500`.

Lakukan pembuatan direktori baru dengan `mkdir /var/www/strix.operation.wise.d05.com`.

Copy file lampiran github ke folder yang telah dibuat `cp -r /root/modul2source-jarkom/strix.operation.wise/ /var/www/strix.operation.wise.d05.com` lalu restart apache dengan `service apache2 restart`.

Pada node Garden dan SSS, kita dapat melakukan testing dengan menggunakan `lynx` pada port 15000 atau 15500 yaitu `lynx http://www.strix.operation.wise.d05.com:15000` atau `lynx http://www.strix.operation.wise.d05.com:15500`.

## Soal 15

#### dengan autentikasi username Twilight dan password opStrix dan file di /var/www/strix.operation.wise.yyy

Di dalam node 'Eden', tambahkan code baru berikut pada file `default-wise-2-15000.conf` dan `default-wise-2-15500.conf` sebagai berikut.

##### Dalam file `default-wise-2-15000.conf`
```bash
<VirtualHost *:15000>
        ServerName strix.operation.wise.d05.com
        ServerAlias www.strix.operation.wise.d05.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/strix.operation.wise.d05.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "var/www/strix.operation.wise.d05.com">
                AuthType Basic
                AuthName "Restricted Content"
                AuthUserFile /etc/apache2/.htpasswd
                Require valid-user
        </Directory>
</VirtualHost>
```

##### Dalam file `default-wise-2-15500.conf`
```bash
<VirtualHost *:15500>
        ServerName strix.operation.wise.d05.com
        ServerAlias www.strix.operation.wise.d05.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/strix.operation.wise.d05.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "var/www/strix.operation.wise.d05.com">
                AuthType Basic
                AuthName "Restricted Content"
                AuthUserFile /etc/apache2/.htpasswd
                Require valid-user
        </Directory>
</VirtualHost>
```
Copy file dengan command `cp /root/default-wise-2-15000.conf /etc/apache2/sites-available/strix.operation.wise.d05.com-15000` dan `cp /root/default-wise-2-15500.conf /etc/apache2/sites-available/strix.operation.wise.d05.com-15500`.

Buat autentikasi baru dengan command berikut sehingga memunculkan file `.htpasswd` pada dengan command `htpasswd -b -c /etc/apache2/.htpasswd Twilight opStrix` lalu restart apache dengan `service apache2 restart`.

Ketika web server `strix.operation.wise.d05.com` diakses, akan diminta authentikasi username dan password.

Input username `Twilight` dan `password opStrix`.

## Soal 16

#### dan setiap kali mengakses IP Eden akan dialihkan secara otomatis ke www.wise.yyy.com.

Konfigurasi file `default-1.conf` pada node 'Eden' menjadi sebagai berikut.

```bash
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.d05.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Copy file tersebut dengan command `cp /root/default-1.conf /etc/apache2/sites-available/000-default.conf`.

Konfigurasi pada file `wise-1.htaccess` sebagai berikut.

```bash
a2enmod rewrite
RewriteEngine On
RewriteBase /
RewriteCond %{HTTP_HOST} ^10\.8\.2\.3$
RewriteRule ^(.*)$ http://www.wise.d05.com/$1 [L,R=301]
```
Inti dari konfigurasi tersebut adalah kita melakukan cek apakah pengaksesan berupa ip ke arah Eden jika hal tersebut terpenuhi maka kita membuat rule untuk melakukan direct ke www.wise.d05.com.

Kemudian copy file dengan command `cp /root/wise-1.htaccess /var/www/wise.d05.com/.htaccess` lalu restart apache dengan `service apache2 restart`.

Lakukan testing pada node client (Garden dan SSS) dengan command `lynx 10.8.2.3`.

## Soal 17

#### Karena website www.eden.wise.yyy.com semakin banyak pengunjung dan banyak modifikasi sehingga banyak gambar-gambar yang random, maka Loid ingin mengubah request gambar yang memiliki substring “eden” akan diarahkan menuju eden.png. Bantulah Agent Twilight dan Organisasi WISE menjaga perdamaian!

Lakukan konfigurasi di dalam node 'Eden' pada file `wise-2.htaccess` sebagai berikut.

```bash
a2enmod rewrite
ewriteEngine On
RewriteBase /
RewriteCond %{REQUEST_URI} !\beden.png\b
RewriteRule eden http://eden.wise.d05.com/public/images/eden.png$1 [L,R=301]
```
Inti dari konfigurasi tersebut adalah kita melakukan cek apakah request mengandung substring 'eden', jika ya maka akan diarahkan ke `http://eden.wise.d05.com/public/images/eden.png`.

Copy file dengan command `cp /root/wise-2.htaccess /var/www/eden.wise.d05.com/.htaccess`.

Konfigurasi pada file `default-wise-7.conf` sebagai berikut.

```bash
<VirtualHost *:80>
        ServerName eden.wise.d05.com
        ServerAlias www.eden.wise.d05.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.d05.com

        <Directory /var/www/eden.wise.d05.com>
                Options +Indexes
                AllowOverride All
        </Directory>

        <Directory /var/www/eden.wise.d05.com/public>
                Options +Indexes
        </Directory>

        Alias "/js" "/var/www/eden.wise.d05.com/public/js"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        ErrorDocument 404 /error/404.html
</VirtualHost>
```

Copy file dengan command `cp /root/default-wise-7.conf /etc/apache2/sites-available/eden.wise.d05.com.conf` lalu restart apache dengan `service apache2 restart`.

Testing pada node client (Garden dan SSS) dengan command `lynx www.eden.wise.d05.com/public/images/abcedendef`.
