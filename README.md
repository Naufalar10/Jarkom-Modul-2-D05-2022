# Jarkom-Modul-2-D05-2022

## 1
## 2
## 3
## 4
## 5
## 6
## 7
## 8
## 9
## 10
## 11
## 12
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

Lakukan testing pada node client (Garden dan SSS) ketika mengakses `lynx http://www.eden.wise.d05.com/js`, maka akan mendapatkan tampilan seperti berikut.

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

Kemudian akan menampilakn hasil berikut.

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

Testing pada node client (Garden dan SSS) dengan command `lynx www.eden.wise.d05.com/public/images/abcedendef`, maka akan muncul tampilan berikut.
