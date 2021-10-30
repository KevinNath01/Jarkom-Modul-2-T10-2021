# Jarkom-Modul-2-T10-2021

PRAKTIKUM MODUL 2 JARINGAN KOMPUTER 2021

Anggota Kelompok T10:<br>
Tri Rizki Yuliawan (05311940000024) <br>
Kevin Nathaniel (05311940000032) <br>
I Gde Ardha Semaranatha Gunasatwika (05311940000034) <br>

# Soal <a name="Soal"></a>

### 6. Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo

**Server EniesLobby**
Melakukan konfigurasi pada `/etc/bind/kaizoku/kaizoku.com`
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.T10.com. root.franky.T10.com.(
                     2021102504         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky.T10.com.
@       IN      A       192.216.2.4 ;IP EniesLobby
www     IN      CNAME   franky.T10.com.
super   IN      A       192.216.2.4 ;IP Skypie
www.super       IN      CNAME   super.franky.T10.com.
ns1     IN      A       192.216.2.3 ;IP Water7
mecha   IN      NS      ns1
```

Melakukan konfigurasi pada `/etc/bind/named.conf.local`
```
zone "franky.T10.com" {
        type master;
        file "/etc/bind/kaizoku/kaizoku.com";
        allow-transfer { 192.216.2.3; };
};
```

***Server Water7***
Melakukan konfigurasi pada `/etc/bind/named.conf.options`
```
options {
        directory "/var/cache/bind";

        // forwarders{
        //      0.0.0.0;
        // };

        //dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035   
        listen-on-v6 { any; };
};
```

Melakukan konfigurasi pada `/etc/bind/named.conf.local`
```
zone "mecha.franky.T10.com" {
    type master;
    file "/etc/bind/sunnygo/mecha.franky.T10.com";
};
```

Melakukan konfigurasi pada `/etc/bind/sunnygo/mecha.franky.T10.com`
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     mecha.franky.T10.com. root.mecha.franky.T10.com.(
                     2021102504         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      mecha.franky.T10.com.
@       IN      A       192.216.2.4 ;IP Skypie
www     IN      CNAME   mecha.franky.T10.com.
```

Dokumentasi hasil

<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/6.png">

### 7. Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie

Melakukan konfigurasi pada `/etc/bind/sunnygo/mecha.franky.T10.com`
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     mecha.franky.T10.com. root.mecha.franky.T10.com.(
                     2021102504         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
; 
@       IN      NS      mecha.franky.T10.com.
@       IN      A       192.216.2.4 ;IP Skypie
www     IN      CNAME   mecha.franky.T10.com.
general IN      A       192.216.2.4
www.general     IN      CNAME   general.mecha.franky.T10.com.
```

Dokumentasi hasil

<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/7.png">

### 8. Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.franky.yyy.com. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada /var/www/franky.yyy.com

***Server Skypie***
Melakukan konfigurasi pada `/etc/apache2/sites-available/franky.T10.com.conf`
```
<VirtualHost *:80>
        ServerAdmin webmaster@localhost

        DocumentRoot /var/www/franky.T10.com
        ServerName franky.T10.com
        ServerAlias www.franky.T10.com

        Alias "/home" "/var/www/franky.T10.com/index.php"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

Membuat direktori untuk file konten pada `/var/www`
```
wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/franky.zip
unzip franky.zip
mkdir franky.T10.com
mv franky/* franky.T10.com
rm -r franky
```

Dokumentasi hasil

<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/8.png">

### 9. Setelah itu, Luffy juga membutuhkan agar url www.franky.yyy.com/index.php/home dapat menjadi menjadi www.franky.yyy.com/home

***Server Skypie*** 
Memberikan Alias pada `/etc/apache2/sites-available/franky.T10.com.conf`
```
<VirtualHost *:80>
        ServerAdmin webmaster@localhost

        DocumentRoot /var/www/franky.T10.com
        ServerName franky.T10.com
        ServerAlias www.franky.T10.com

        Alias "/home" "/var/www/franky.T10.com/index.php"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

Dokumentasi hasil

<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/9.png">

### 10. Setelah itu, pada subdomain www.super.franky.yyy.com, Luffy membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/super.franky.yyy.com

***Server Skypie***
Melakukan konfigurasi pada `/etc/apache2/sites-available/super.franky.T10.com`
```
<VirtualHost *:80>
        ServerAdmin webmaster@localhost

        DocumentRoot /var/www/super.franky.T10.com
        ServerName super.franky.T10.com
        ServerAlias www.super.franky.T10.com

        <Directory /var/www/super.franky.T10.com/public>
                Options +Indexes
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        Alias "/js" "/var/www/super.franky.T10.com/public/js"

        ErrorDocument 404 /error/404.html

</VirtualHost>
```

Membuat direktori untuk file konten pada `/var/www`
```
wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/super.fra$
unzip super.franky.zip
mkdir super.franky.T10.com
mv super.franky/* super.franky.T10.com
rm -r super.franky
```

Dokumentasi hasil

<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/10.png">
