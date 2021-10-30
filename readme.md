# Jarkom-Modul-2-T10-2021

PRAKTIKUM MODUL 2 JARINGAN KOMPUTER 2021

Anggota Kelompok T10:<br>
Tri Rizki Yuliawan (05311940000024) <br>
Kevin Nathaniel (05311940000032) <br>
I Gde Ardha Semaranatha Gunasatwika (05311940000034) <br>

# Soal <a name="Soal"></a>

### 6. Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo

***Server EniesLobby***
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

<img src="hasil/6.png">

### 7. Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie