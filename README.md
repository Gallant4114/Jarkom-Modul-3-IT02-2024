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
```

### Tybur - DHCP Server
```
auto eth0
iface eth0 inet static
	address 192.234.4.3
	netmask 255.255.255.0
	gateway 192.234.4.1
```

### Fritz - DNS Server
```
auto eth0
iface eth0 inet static
	address 192.234.4.2
	netmask 255.255.255.0
	gateway 192.234.4.1
```

### Warhammer - Database Server
```
auto eth0
iface eth0 inet static
	address 192.234.2.4
	netmask 255.255.255.0
	gateway 192.234.2.1
```

### Beast - Load Balancer (Laravel)
```
auto eth0
iface eth0 inet static
	address 192.234.2.2
	netmask 255.255.255.0
	gateway 192.234.2.1
```

### Colossal - Load Balancer (PHP)
```
auto eth0
iface eth0 inet static
	address 192.234.2.3
	netmask 255.255.255.0
	gateway 192.234.2.1
```

### Annie - Laravel Worker
```
auto eth0
iface eth0 inet static
	address 192.234.1.2
	netmask 255.255.255.0
	gateway 192.234.1.1
```

### Bertholdt - Laravel Worker
```
auto eth0
iface eth0 inet static
	address 192.234.1.3
	netmask 255.255.255.0
	gateway 192.234.1.1
```

### Reiner - Laravel Worker
```
auto eth0
iface eth0 inet static
	address 192.234.1.4
	netmask 255.255.255.0
	gateway 192.234.1.1
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

## Soal
> Pulau Paradis telah menjadi tempat yang damai selama 1000 tahun, namun kedamaian tersebut tidak bertahan selamanya. Perang antara kaum Marley dan Eldia telah mencapai puncak. Kaum Marley yang dipimpin oleh Zeke, me-register domain name **marley.yyy.com** untuk worker Laravel mengarah pada **Annie**. Namun ternyata tidak hanya kaum Marley saja yang berinisiasi, kaum Eldia ternyata sudah mendaftarkan domain name **eldia.yyy.com** untuk worker PHP **(0)** mengarah pada **Armin**.

Dalam modul ini diperlukan instalasi `bind9` untuk DNS server, dalam hal ini adalah node `Fritz`. Caranya adalah sebagai berikut,

1. Membuka console pada node `Fritz`
2. Menjalankan command untuk install `bind9` dengan diawali update.

```
apt-get update
apt-get install bind9 -y
```


