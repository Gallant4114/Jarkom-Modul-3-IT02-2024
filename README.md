# Praktikum Modul 03 Komunikasi Data dan Jaringan Komputer
## Kelompok IT02
### Anggota Kelompok :
|             Nama              |     NRP    |
|-------------------------------|------------|
| Gallant Damas Hayuaji         | 5027231037 |
| Danar Bagus Rasendriya        | 5027231055 |

***

## Topologi

![image](https://github.com/user-attachments/assets/76a337e8-ec93-412b-a626-07f03ab8fcb0)

## Configure Network Adapters
![Screenshot 2024-10-27 at 17 21 55](https://github.com/user-attachments/assets/c1ae7447-1e08-460c-a0e4-a627b503be77)
Pada topologi sebelumnya, sempat tidak bisa connect dengan semua switch, ada dua upaya yang bisa dilakukan yaitu:
1. Configure dengan menambah jumlah network adaptors langsung pada router.
2. Membuat Docker Container template baru, lalu menambahkan jumlah network adapter.

***
## Konfigurasi
|Node|Kategori|Konfigurasi IP|
|--|--|--|
|Paradis|Router (DHCP Relay)|Dynamic|
|Tybur|DHCP Server|Static|
|Fritz|DNS Server|Static|
|Warhammer|Database Server|Static|
|Beast|Load Balancer (Laravel)|Static|
|Colossal|Load Balancer (PHP)|Static|
|Annie|Laravel Worker|Static|
|Bertholdt|Laravel Worker|Static|
|Reiner|Laravel Worker|Static|
|Armin|PHP Worker|Static|
|Eren|PHP Worker|Static|
|Mikasa|PHP Worker|Static|
|Zeke|Client|Dynamic|
|Erwin|Client|Dynamic|

Image yang digunakan adalah `danielcristh0/debian-buster:1.1`

Prefix IP `192.234`

### Paradis - Router (DHCP Relay)

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.234.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.234.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.234.3.1
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 192.234.4.1
	netmask 255.255.255.0

up echo 'nameserver 192.168.122.1' > /etc/resolv.conf
up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.234.0.0/16
```

### Tybur - DHCP Server
```
auto eth0
iface eth0 inet static
	address 192.234.4.3
	netmask 255.255.255.0
	gateway 192.234.4.1

up echo 'nameserver 192.168.122.1' > /etc/resolv.conf
```

### Fritz - DNS Server
```
auto eth0
iface eth0 inet static
	address 192.234.4.2
	netmask 255.255.255.0
	gateway 192.234.4.1

up echo 'nameserver 192.168.122.1' > /etc/resolv.conf
```

### Warhammer - Database Server
```
auto eth0
iface eth0 inet static
	address 192.234.3.4
	netmask 255.255.255.0
	gateway 192.234.3.1

up echo 'nameserver 192.168.122.1' > /etc/resolv.conf
```

### Beast - Load Balancer (Laravel)
```
auto eth0
iface eth0 inet static
	address 192.234.3.2
	netmask 255.255.255.0
	gateway 192.234.3.1

up echo 'nameserver 192.168.122.1' > /etc/resolv.conf
```

### Colossal - Load Balancer (PHP)
```
auto eth0
iface eth0 inet static
	address 192.234.3.3
	netmask 255.255.255.0
	gateway 192.234.3.1

up echo 'nameserver 192.168.122.1' > /etc/resolv.conf
```

### Annie - Laravel Worker
```
auto eth0
iface eth0 inet static
	address 192.234.1.2
	netmask 255.255.255.0
	gateway 192.234.1.1

up echo 'nameserver 192.168.122.1' > /etc/resolv.conf
```

### Bertholdt - Laravel Worker
```
auto eth0
iface eth0 inet static
	address 192.234.1.3
	netmask 255.255.255.0
	gateway 192.234.1.1

up echo 'nameserver 192.168.122.1' > /etc/resolv.conf
```

### Reiner - Laravel Worker
```
auto eth0
iface eth0 inet static
	address 192.234.1.4
	netmask 255.255.255.0
	gateway 192.234.1.1

up echo 'nameserver 192.168.122.1' > /etc/resolv.conf
```

### Armin - PHP Worker
```
auto eth0
iface eth0 inet static
	address 192.234.2.2
	netmask 255.255.255.0
	gateway 192.234.2.1
```

### Eren - PHP Worker
```
auto eth0
iface eth0 inet static
	address 192.234.2.3
	netmask 255.255.255.0
	gateway 192.234.2.1
```

### Mikasa - PHP Worker
```
auto eth0
iface eth0 inet static
	address 192.234.2.4
	netmask 255.255.255.0
	gateway 192.234.2.1
```

### Zeke - Client
```
auto eth0
iface eth0 inet dhcp
```

### Erwin - Client
```
auto eth0
iface eth0 inet dhcp
```
***

## Soal 0
> Pulau Paradis telah menjadi tempat yang damai selama 1000 tahun, namun kedamaian tersebut tidak bertahan selamanya. Perang antara kaum Marley dan Eldia telah mencapai puncak. Kaum Marley yang dipimpin oleh Zeke, me-register domain name **marley.yyy.com** untuk worker Laravel mengarah pada **Annie**. Namun ternyata tidak hanya kaum Marley saja yang berinisiasi, kaum Eldia ternyata sudah mendaftarkan domain name **eldia.yyy.com** untuk worker PHP **(0)** mengarah pada **Armin**.

Dalam modul ini diperlukan instalasi `bind9` untuk DNS server, dalam hal ini adalah node `Fritz`. Caranya adalah sebagai berikut,

1. Membuka console pada node `Fritz`
2. Menjalankan command untuk install `bind9` dengan diawali update.

```
apt-get update
apt-get install bind9 -y
```

Setelah berhasil install, jalankan service tersebut
```
service bind9 start
```

Dengan menggunakan text editor, lakukan konfigurasi pada `/etc/bind/named.conf.local`
```
nano /etc/bind/named.conf.local
```
Masukkan konfigurasi berikut
```
zone "marley.it02.com" {
     type master;
     file "/etc/bind/it02/marley.it02.com";
};

zone "eldia.it02.com" {
      type master;
      file "/etc/bind/it02/eldia.it02.com";
};
```
Membuat direktori
```
mkdir /etc/bind/it02
```
Buka menggunakan text editor dan lakukan konfigurasi untuk `marley.it02.com` dan `eldia.it02.com`

```
nano /etc/bind/it02/marley.it02.com
```
Masukkan konfigurasi berikut untuk `marley.it02.com`

```
$TTL    604800
@       IN      SOA     marley.it02.com. root.marley.it02.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      marley.it02.com.
@       IN      A       192.234.1.2
www	IN	CNAME	marley.it02.com.
```

Lakukan hal yang serupa untuk `eldia.it02.com`
```
nano /etc/bind/it02/eldia.it02.com
```
```
$TTL    604800
@       IN      SOA     eldia.it02.com. root.eldia.it02.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      eldia.it02.com.
@       IN      A       192.234.2.2
www	IN	CNAME	eldia.it02.com.
```

Selanjutnya adalah memastikan bahwa client dapat terhubung ke internet.
```
nano /etc/bind/named.conf.options
```
Masukkan konfigurasi berikut
```
options {
        directory "/var/cache/bind";

	forwarders {
	   192.168.122.1;
	};

        allow-query{any;};
	auth-nxdomain no;
      	listen-on-v6 { any; };
  };
```

Setelah melakukan konfigurasi, restart bind9
```
service bind9 restart
```

## Soal 1
> Lakukan konfigurasi sesuai dengan peta yang sudah diberikan.

IP Address yang digunakan client harus sesuai dengan DHCP Server (`Tybur`). Oleh karena itu, perlu dilakukan setting untuk DHCP Server.

### Setup Tybur - DHCP Server

Pertama adalah melakukan update dan menginstall dependencies yang diperlukan pada web console `Tybur`.
```
apt-get update
apt-get install isc-dhcp-server -y
```

Jalankan command berikut
```
nano /etc/default/isc-dhcp-server
```
Masukkan konfigurasi
```
INTERFACES="eth0"
```
Jika sudah, lanjut ke konfigurasi berikutnya
```
nano /etc/dhcp/dhcpd.conf
```
Masukkan konfigurasi
```
subnet 192.234.1.0 netmask 255.255.255.0 {
	option routers 192.234.1.1;
	option broadcast-address 192.234.1.255;
	option domain-name-servers 192.234.4.2;
}

subnet 192.234.2.0 netmask 255.255.255.0 {
	option routers 192.234.2.1;
	option broadcast-address 192.234.2.255;
	option domain-name-servers 192.234.4.2;
}

subnet 192.234.3.0 netmask 255.255.255.0 {}

subnet 192.234.4.0 netmask 255.255.255.0 {}
```
Lakukan restart servis `isc-dhcp-server`
```
service isc-dhcp-server restart
```

### Setup Paradis - DHCP Relay

Buka web console `Paradis` lalu update dan install dependencies yang diperlukan
```
apt-get update
apt-get install isc-dhcp-relay -y
```

Jalankan service tersebut
```
service isc-dhcp-server start
```

Masuk ke text editor `isc-dhcp-relay`
```
nano /etc/default/isc-dhcp-relay
```

Masukkan konfigurasi. IP Address menyesuaikan dari DHCP Server Tybur
```
SERVERS="192.234.4.3"
INTERFACES="eth1 eth2 eth3 eth4"
OPTIONS=""
```
Masuk ke text editor `/etc/sysctl.conf`
```
nano /etc/sysctl.conf
```
Masukkan konfigurasi
```
net.ipv4.ip_forward=1
```

Lakukan restart setelah selesai konfigurasi
```
service isc-dhcp-relay restart
```

## Soal 2
> **Client** yang melalui bangsa marley mendapatkan range IP dari [prefix IP].1.05 - [prefix IP].1.25 dan [prefix IP].1.50 - [prefix IP].1.100 **(2)**

Perlu melakukan penambahan konfigurasi pada **DHCP Server Tybur**.

Buka text editor pada web console `Tybur`
```
nano /etc/dhcp/dhcpd.conf
```

Tambahkan konfigurasi seperti berikut
```
subnet 192.234.1.0 netmask 255.255.255.0 {
	range 192.234.1.05 192.234.1.25;
	range 192.234.1.50 192.234.1.100;
	option routers 192.234.1.0;
	option broadcast-address 192.234.1.255;
}
```

Lakukan restart
```
service idc-dhcp-server restart
```

## Soal 3
> **Client** yang melalui bangsa eldia mendapatkan range IP dari [prefix IP].2.09 - [prefix IP].2.27 dan [prefix IP].2 .81 - [prefix IP].2.243 **(3)**

Masuk ke web Console **DHCP Server Tybur**

Buka text editor
```
nano /etc/dhcp/dhcpd.conf
```

Masukkan konfigurasi
```
subnet 192.234.2.0 netmask 255.255.255.0 {
	range 192.234.2.09 192.234.2.27;
	range 192.234.2.81 192.234.2.243;
	option routers 192.234.2.0;
	option broadcast-address 192.234.2.255;
}
```

Lakukan restart
```
service isc-dhcp-server-restart
```

Setelah konfigurasi selesai, perlu dilakukan pengujian pada client untuk mengecek apakah IP Address berhasil.

Caranya adalah membuka console pada masing-masing client `Zeke` dan `Erwin`.

Menjalankan command pada web console client
```
ip a
```

## Soal 4
> Client mendapatkan DNS dari keluarga **Fritz** dan dapat terhubung dengan internet melalui DNS tersebut (4)

Membuka web console pada node `Tybur`

Buka text editor
```
nano /etc/dhcp/dhcpd.conf
```
Masukkan konfigurasi berikut
```
subnet 192.234.1.0 netmask 255.255.255.0 {
  	range 192.234.1.05 192.234.1.25;
	range 192.234.1.50 192.234.1.100;
	option routers 192.234.1.1;
	option broadcast-address 192.234.1.255;
	option domain-name-servers 192.234.4.2;
}

subnet 192.234.2.0 netmask 255.255.255.0 {
	range 192.234.2.09 192.234.2.27;
	range 192.234.2.81 192.234.2.243;
	option routers 192.234.2.1;
	option broadcast-address 192.234.2.255;
	option domain-name-servers 192.234.4.2;
}

subnet 192.234.3.0 netmask 255.255.255.0 {}

subnet 192.234.4.0 netmask 255.255.255.0 {}
```

Selanjutnya adalah membuka web console pada node `Fritz`
```
nano /etc/bind/named.conf.options
```
Masukkan konfigurasi agar bisa terhubung ke internet
```
options {
        directory "/var/cache/bind";

        forwarders {
	192.168.122.1;
        };

        allow-query{any;};
        auth-nxdomain no;

        listen-on-v6 { any; };
};
```
Jika sudah, lakukan restart service
```
service bind9 restart
```

Selanjutnya adalah melakukan pengujian untuk memastikan Marley dan Eldia dapat tersambung ke internet.

Buka pada masing-masing web console `Zeke` dan `Erwin` lalu jalankan command berikut
```
ping marley.it02.com -c 4
ping eldia.it02.com -c 4
```

## Soal 5
> Dikarenakan keluarga **Tybur** tidak menyukai kaum **eldia**, maka mereka hanya meminjamkan ip address ke kaum **eldia** selama 6 menit. Namun untuk kaum **marley**, keluarga **Tybur** meminjamkan ip address selama 30 menit. Waktu maksimal dialokasikan untuk peminjaman alamat IP selama 87 menit. **(5)**

Buka web console node `Tybur` dan jalankan command berikut
```
nano /etc/dhcp/dhcpd.conf
```

Masukkan konfigurasi berikut
```
subnet 192.234.1.0 netmask 255.255.255.0 {
  	range 192.234.1.05 192.234.1.25;
	range 192.234.1.50 192.234.1.100;
	option routers 192.234.1.1;
	option broadcast-address 192.234.1.255;
	option domain-name-servers 192.234.4.2;
	default-lease-time 1800;
	max-lease-time 5220;
}

subnet 192.234.2.0 netmask 255.255.255.0 {
	range 192.234.2.09 192.234.2.27;
	range 192.234.2.81 192.234.2.243;
	option routers 192.234.2.1;
	option broadcast-address 192.234.2.255;
	option domain-name-servers 192.234.4.2;
	default-lease-time 360;
	max-lease-time 5220;
}

subnet 192.234.3.0 netmask 255.255.255.0 {}

subnet 192.234.4.0 netmask 255.255.255.0 {}
```

Lakukan restart setelah konfigurasi
```
service isc-dhcp-server restart
```

## Soal 6
> **Armin** berinisiasi untuk memerintahkan setiap worker PHP untuk melakukan konfigurasi virtual host untuk website berikut https://intip.in/BangsaEldia dengan menggunakan php 7.3 **(6)**

Untuk melakukan konfigurasi, maka perlu membuka web console untuk node PHP Worker, yaitu `Armin`, `Eren`, dan `Mikasa`.

- Update dan Install Dependencies
```
apt-get update
apt-get install lynx nginx wget unzip php7.3 php-fpm -y
```

- Jalankan service PHP dan Nginx
```
service php7.3-fpm start
service nginx start
```

- Buat direktori untuk simpan file
```
mkdir -p /var/www/html/download/
```

- Download File dari Google Drive
```
wget --no-check-certificate 'https://drive.google.com/uc?export=download&id=1TvebIeMQjRjFURKVtA32lO9aL7U2msd6' -O /var/www/html/download/eldia.zip
```

- Unzip dan letakkan pada `/var/www/html/download/`
```
unzip /var/www/html/download/eldia.zip -d /var/www/eldia.it02.com
```

- Pindahkan file yang sudah diextract
```
mv /var/www/html/download/eldia/modul-3/* /var/www/html/
```

- Hapus direktori download
```
rm -rf /var/www/html/download/
```

- Buka text editor untuk konfigurasi Nginx
```
nano /etc/nginx/sites-available/eldia.it02.com
```

- Masukkan konfigurasi
```
server {
	listen 80;
	server_name eldia.it02.com;

	root /var/www/eldia.it02.com;

	index index.php index.html index.htm;

	server_name _;

	location / {
		try_files \$uri \$uri/ /index.php?\$query_string;
	}

	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
	}

	error_log /var/log/nginx/jarkom-it02_error.log;
	access_log /var/log/nginx/jarkom-it02_access.log;
}
```

- Atur konfigurasi menjadi enabled
```
ln -s /etc/nginx/sites-available/eldia.it02.com /etc/nginx/sites-enabled
```

- Hapus file `default`
```
rm /etc/nginx/sites-enabled/default
```

- Restart service PHP dan Nginx
```
service php7.3-fpm restart
service nginx restart
```

## Soal 7
> Dikarenakan Armin sudah mendapatkan kekuatan titan colossal, maka bantulah kaum **eldia** menggunakan **colossal** agar dapat bekerja sama dengan baik. Kemudian lakukan testing dengan 6000 request dan 200 request/second. **(7)**

- Buka web console `Load Balancer Colossal` lalu install dependencies
```
apt-get update
apt-get install lynx nginx php7.3 php-fpm apache2-utils -y 
```

- Jalankan service `PHP` dan `Nginx`
```
service php7.3-fpm start
service nginx start
```

- Buka text editor
```
nano /etc/nginx/sites-available/load-balancer-it02
```

- Masukkan konfigurasi
```
upstream roundrobin {
    server 192.234.2.2;
    server 192.234.2.3;
    server 192.234.2.4;
}

server {
    listen 80;

    server_name _;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://roundrobin;
	proxy_http_version 1.1;
	proxy_set_header Upgrade $http_upgrade;
	proxy_set_header Connection 'upgrade';
	proxy_set_header Host $host;
	proxy_cache_bypass $http_upgrade
    }
}
```

- Enable `/etc/nginx/sites-enabled`
```
ln -s /etc/nginx/sites-available/load-balancer-it02 /etc/nginx/sites-enabled
```

- Hapus file `default`
```
rm /etc/nginx/sites-enabled/default
```

- Restart service `PHP` dan `Nginx`
```
service php7.3-fpm restart
service nginx restart
```
