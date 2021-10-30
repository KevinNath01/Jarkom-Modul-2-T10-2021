# Jarkom-Modul-2-T10-2021

PRAKTIKUM MODUL 2 JARINGAN KOMPUTER 2021

Anggota Kelompok T10:<br>
Tri Rizki Yuliawan (05311940000024) <br>
Kevin Nathaniel (05311940000032) <br>
I Gde Ardha Semaranatha Gunasatwika (05311940000034) <br>

# Soal <a name="Soal"></a>

### 1. EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet 
Pada soal ini kita akan membuat node-node dan akan menghubungkannya ke switch dan ke router Foosha untuk bisa mengakses ke internet

<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/1.png">

Kemudian pada router Foosha akan dilakukan setting konfigurasi dengan command ```iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.216.0.0/16``` . Kemudian pada setiap nodenya dilakukan setting konfigurasi di resolv.conf dengan command ```echo nameserver 192.168.122.1 > /etc/resolv.conf``` dimana ip address didapatkan dari resolv.conf di Foosha. Kemudian kita lakukan pengecekan pada masing-masing node. Pengecekan dilakukan dengan cara ping ke google.com.

<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/1a.png">
<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/1b.png">
<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/1c.png">
<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/1d.png">
<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/1e.png">

### 2. Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku

** EniesLobby ** <br>
Untuk membuat website , akan dilakukan konfigurasi pada named.conf.local untuk mendaftarkan website tersebut dengan command <br>
``` 
zone "franky.T10.com" {
	type master;
	file "/etc/bind/kaizoku/kaizoku.com";
};'
```
Kemudian kita akan membuat file baru untuk konfigurasi websitenya dengan foldernya bernama kaizoku dan file webnya bernama kaizoku.com 
```
mkdir /etc/bind/kaizoku
cp /etc/bind/db.local /etc/bind/kaizoku/kaizoku.com
```
isi file dari kaizoku.com adalah sebagai berikut
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.T10.com. root.franky.T10.com.(
                     2021102501         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky.T10.com.
@       IN      A       192.216.2.2 ;IP EniesLobby
www	IN	CNAME	franky.T10.com.'
```
kemudian untuk mengaktifkan webnya dijalankan perintah ```service bind9 restart```

<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/2.png">

### 3. Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie

Untuk membuat subdomain www.super.franky.yyy.com yang diarahkan ke ip skypie maka akan dilakukan konfigurasi ke file kaizoku.com sebagai berikut
 ```
 ;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.T10.com. root.franky.T10.com.(
                     2021102501         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky.T10.com.
@       IN      A       192.216.2.2 ;IP EniesLobby
www	    IN	    CNAME	  franky.T10.com.
super	  IN	    A  	    192.216.2.4 ;IP Skypie
www.super	IN	CNAME	super.franky.T10.com.
```
Kemudian lakukan command ```service bind9 restart``` untuk mendeploy ulang web dengan konfigurasi yang baru 

<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/3.png">

### 4. Buat reverse domain untuk domain utama

Untuk membuat reverse domain dari domain utama , maka perlu dibuat file konfigurasi baru di named.conf.local, dimana ip yang dijadikan reverse address adalah 3 bit pertama yang dibalik (IP EnniesLobby) sebagai berikut
```
zone "2.216.192.in-addr.arpa" {
    type master;
    file "/etc/bind/kaizoku/2.216.192.in-addr.arpa";
};
```
kemudian dilakukan juga konfigurasi baru jika reverse ip yang diakses di folder kaizoku sebagai berikut
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.T10.com. root.franky.T10.com.(
                     2021102501         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.216.192.in-addr.arpa.       IN      NS      franky.T10.com.
2                             IN      PTR     franky.T10.com.
```

Untuk mengaktifkan konfigurasi yang baru dibuat dilakukan command ```service bind9 restart```, untuk mengecek apakah reverse ip ini mengarah ke franky.t10.com , gunakan command ```host -t PTR 192.216.2.2```

<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/4.png">

### 5. Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama 
Untuk mengerjakan soal ini maka perlu dilakukan konfigurasi di EnniesLobby dan Water 7
**EnniesLobby**
Melakukan konfigurasi pada named.conf.local untuk memberitahu Water7 jika Ennieslobby sedang down , sehingga prosesnya bisa dialihkan ke Water7
```
zone "franky.T10.com" {
        type master;
	notify yes;
	also-notify { 192.216.2.3; }; //IP Water7
	allow-transfer { 192.216.2.3; };
        file "/etc/bind/kaizoku/kaizoku.com";
};

zone "2.216.192.in-addr.arpa" {
    type master;
    file "/etc/bind/kaizoku/2.216.192.in-addr.arpa";
};
```

**Water7**
Melakukan konfigurasi pada named.conf.local untuk menyatakan water7 sebagai DNS slave dari EnniesLobby
```
zone "franky.T10.com" {
    type slave;
    masters { 192.216.2.2; }; // Masukan IP EniesLobby tanpa tanda petik
    file "/var/lib/bind/kaizoku.com";
};
```
Untuk mengecek apakah konfigurasi ini sudah berjalan maka pada EnniesLobby akan menghentikan service bind9 ```service bind9 stop```
sedangkan pada Water7 service bind9 akan di start ```service bind9 restart``` dan akan dicoba untuk mengakses franky.T10.com dari loguetown

<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/5a.png">
<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/5b.png">

### 6. Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo

**Server EniesLobby** <br>
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

***Server Water7*** <br>
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

***Server Skypie*** <br>
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

***Server Skypie***  <br>
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

***Server Skypie*** <br>
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
wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/super.franky.zip
unzip super.franky.zip
mkdir super.franky.T10.com
mv super.franky/* super.franky.T10.com
rm -r super.franky
```

Dokumentasi hasil

<img src="https://github.com/KevinNath01/Jarkom-Modul-2-T10-2021/blob/main/SS%20Hasil/10.png">
