# Jarkom-Modul-2-IT11-2024

## Kelompok IT11
| NRP | Nama |
| ------ | ------ |
| 5027211049 | Tridiktya Hardani Putra |
| 5027221008 | Jeany Aurellia Putri Dewati |

## Inisiasi
Sebelum memulai, ada beberapa hal yang harus dipersiapkan.

### Script .bashrc Erangel
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.69.0.0/16
echo nameserver 192.168.122.1 > /etc/resolv.conf
```
Script tersebut dijalankan secara otomatis agar Erangel dan nodes lainnya (jika mengonfigurasi /etc/resolv.conf dengan alamat IP tersebut) dapat mengakses jaringan luar.

### Script .bashrc Pochinki dan Georgopol 
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
```
Script tersebut dijalankan secara otomatis agar DNS Master (Pochinki) dan Slave (Georgopol) dapat mengakses jaringan luar, sehingga memungkinkan untuk melakukan update dan instalasi package, tepatnya bind9.

### Script .bashrc Clients (Gatka, Quarry, dan Shelter)
```
echo nameserver 10.69.1.2 >> /etc/resolv.conf
```
Script tersebut dijalankan secara otomatis agar Clients dapat mengakses jaringan yang terhubung sesuai dengan konfigurasi dari Pochinki selaku DNS Master.

## SOAL NO 1
Untuk membantu pertempuran di Erangel, kamu ditugaskan untuk membuat jaringan komputer yang akan digunakan sebagai alat komunikasi. Sesuaikan rancangan Topologi dengan rancangan dan pembagian yang berada di link yang telah disediakan, dengan ketentuan nodenya sebagai berikut :
* DNS Master akan diberi nama Pochinki, sesuai dengan kota tempat dibuatnya server tersebut
* Karena ada kemungkinan musuh akan mencoba menyerang Server Utama, maka buatlah DNS Slave Georgopol yang mengarah ke Pochinki
* Markas pusat juga meminta dibuatkan tiga Web Server yaitu Severny, Stalber, dan Lipovka. Sedangkan Mylta akan bertindak sebagai Load Balancer untuk server-server tersebut

### Topologi
![topologi_no1](/images/topologi.png)

### Konfigurasi
#### Erangel (Router)
```auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.69.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.69.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.69.3.1
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 10.69.4.1
	netmask 255.255.255.0
```

#### Pochinki (DNS Master)
```
auto eth0
iface eth0 inet static
	address 10.69.1.2
	netmask 255.255.255.0
	gateway 10.69.1.1
```

#### Quarry (Client)
```
auto eth0
iface eth0 inet static
	address 10.69.2.2
	netmask 255.255.255.0
	gateway 10.69.2.1
```

#### Shelter (Client)
```
auto eth0
iface eth0 inet static
	address 10.69.2.3
	netmask 255.255.255.0
	gateway 10.69.2.1
```

#### Georgopol (DNS Slave)
```
auto eth0
iface eth0 inet static
	address 10.69.3.2
	netmask 255.255.255.0
	gateway 10.69.3.1
```

#### Gatka (Client)
```
auto eth0
iface eth0 inet static
	address 10.69.3.3
	netmask 255.255.255.0
	gateway 10.69.3.1
```

#### Severny (Web Server)
```
auto eth0
iface eth0 inet static
	address 10.69.4.2
	netmask 255.255.255.0
	gateway 10.69.4.1
```

#### Stalber (Web Server)
```
auto eth0
iface eth0 inet static
	address 10.69.4.3
	netmask 255.255.255.0
	gateway 10.69.4.1
```

#### Lipovka (Web Server)
```
auto eth0
iface eth0 inet static
	address 10.69.4.4
	netmask 255.255.255.0
	gateway 10.69.4.1
```

#### Mylta (Load Balancer)
```
auto eth0
iface eth0 inet static
	address 10.69.4.5
	netmask 255.255.255.0
	gateway 10.69.4.1
```

## SOAL NO 2
Karena para pasukan membutuhkan koordinasi untuk mengambil airdrop, maka buatlah sebuah domain yang mengarah ke Stalber dengan alamat airdrop.xxxx.com dengan alias www.airdrop.xxxx.com dimana xxxx merupakan kode kelompok. Contoh : airdrop.it01.com

### Script (Pochinki)
``` bash
echo 'zone "airdrop.it11.com" {
    type master;
    notify yes;
    file "/etc/bind/jarkom/airdrop.it11.com";
};' > /etc/bind/named.conf.local

mkdir /etc/bind/jarkom

cp /etc/bind/db.local /etc/bind/jarkom/airdrop.it11.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     airdrop.it11.com. root.airdrop.it11.com. (
                        2023101001      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@       IN      NS      airdrop.it11.com.
@       IN      A       10.69.4.3     ; IP Stalber
www     IN      CNAME   airdrop.it11.com.' > /etc/bind/jarkom/airdrop.it11.com

service bind9 restart
```
Script dapat dijalankan untuk mengonfigurasi DNS, tepatnya pada file konfigurasi **/etc/bind/named.conf.local** dan juga **/etc/bind/jarkom/airdrop.it11.com** supaya domain **airdrop.it11.com** dan **aliasnya** dikenali oleh DNS sebagai domain dari **Stalber (10.69.4.3)**.

## SOAL NO 3
Para pasukan juga perlu mengetahui mana titik yang sedang di bombardir artileri, sehingga dibutuhkan domain lain yaitu redzone.xxxx.com dengan alias www.redzone.xxxx.com yang mengarah ke Severny

### Script (Pochinki)
```bash
echo 'zone "redzone.it11.com" {
    type master;
    notify yes;
    file "/etc/bind/jarkom/redzone.it11.com";
};' >> /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/redzone.it11.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it11.com. root.redzone.it11.com. (
                        2023101001      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@       IN      NS      redzone.it11.com.
@       IN      A       10.69.4.2     ; IP Severny
www     IN      CNAME   redzone.it11.com.' > /etc/bind/jarkom/redzone.it11.com

service bind9 restart
```
Script dapat dijalankan untuk mengonfigurasi DNS, tepatnya pada file konfigurasi **/etc/bind/named.conf.local** dan juga **/etc/bind/jarkom/redzone.it11.com** supaya domain **redzone.it11.com** dan **aliasnya** dikenali oleh DNS sebagai domain dari **Severny (10.69.4.2)**.

## SOAL NO 4
Markas pusat meminta dibuatnya domain khusus untuk menaruh informasi persenjataan dan suplai yang tersebar. Informasi persenjataan dan suplai tersebut mengarah ke Mylta dan domain yang ingin digunakan adalah loot.xxxx.com dengan alias www.loot.xxxx.com

### Script (Pochinki)
```bash
echo 'zone "loot.it11.com" {
    type master;
    notify yes;
    file "/etc/bind/jarkom/loot.it11.com";
};' >> /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/loot.it11.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     loot.it11.com. root.loot.it11.com. (
                        2023101001      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@       IN      NS      loot.it11.com.
@       IN      A       10.69.4.5     ; IP Mylta
www     IN      CNAME   loot.it11.com.' > /etc/bind/jarkom/loot.it11.com

service bind9 restart
```
Script dapat dijalankan untuk mengonfigurasi DNS, tepatnya pada file konfigurasi **/etc/bind/named.conf.local** dan juga **/etc/bind/jarkom/loot.it11.com** supaya domain **loot.it11.com** dan **aliasnya** dikenali oleh DNS sebagai domain dari **Mylta (10.69.4.5)**.

## SOAL NO 5

### Script (Clients - Gatka, Quarry, dan Shelter)
```bash
ping airdrop.it11.com -c 4
ping www.airdrop.it11.com -c 4

ping redzone.it11.com -c 4
ping www.redzone.it11.com -c 4

ping loot.it11.com -c 4
ping www.loot.it11.com -c 4
```
Script dapat dijalankan untuk menguji konektivitas masing-masing domain dan aliasnya dengan clients.

### Hasil
Screenshot di bawah ini merupakan hasil dari menjalankan script di atas.
#### Gatka
![no5_gatka_1](/images/no5_gatka_1.png)

![no5_gatka_2](/images/no5_gatka_2.png)

#### Quarry
![no5_quarry_1](/images/no5_quarry_1.png)

![no5_quarry_2](/images/no5_quarry_2.png)

#### Shelter
![no5_shelter_1](/images/no5_shelter_1.png)

![no5_shelter_2](/images/no5_shelter_2.png)

## SOAL NO 6
Beberapa daerah memiliki keterbatasan yang menyebabkan hanya dapat mengakses domain secara langsung melalui alamat IP domain tersebut. Karena daerah tersebut tidak diketahui secara spesifik, pastikan semua komputer (client) dapat mengakses domain redzone.xxxx.com melalui alamat IP Severny (Notes : menggunakan pointer record)

### Setting pada Pochinki
Lakukan konfigurasi pada /etc/bind/named.conf.local dengan menambahkan zone.

![6_1](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/5c9e277c-07f9-4aa6-9f3d-ce45513c099e)

Copy db.local ke file .arpa

![6_2](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/7d5586bc-36fb-4e7e-9dda-42ba8db3cefc)

Lakukan konfigurasi pada file .arpa

![6_3](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/a95524c2-9c1f-4948-b2c7-47c8c31fa44f)

restart bind9 

![6_4](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/5267dec7-9144-4b08-8371-5c3311fe8671)


### Lakukan pada klien

Lakukan command ini untuk melakukan pengecekan

![image](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/f2c528a6-0c0a-4964-849b-33c7fdd52125)

Hasil yang didapatkan akan seperti ini

![6_5](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/dbab26f4-0be0-476d-92b3-c16ef7230588)

## SOAL NO 7
Akhir-akhir ini seringkali terjadi serangan siber ke DNS Server Utama, sebagai tindakan antisipasi kamu diperintahkan untuk membuat DNS Slave di Georgopol untuk semua domain yang sudah dibuat sebelumnya
### Setting pada Pochinki

setting named.conf.local

![7](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/93db3681-b45b-43af-bcc4-d69daed0dc23)

restart bind9

`service bind9 restart`

### Setting pada Geogorpol

setting named.conf.local
![7_1](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/298669cc-a64f-418d-b23b-8c242ea21c53)

lakukan konfigurasi sebagai berikut pada /var/lib/bind dan lakukan restart bind9

![7_2](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/ae8a87ec-6cf9-420c-9d00-4837a3c79d83)

![7_3](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/d79c1a7e-1c74-45e7-9794-5b5761a016a5)

![7_3](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/044f8eac-95e4-4fd8-bbac-0d3fb3025275)

### Lakukan pada Klien

![7_5](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/cf83ced9-f9f5-4615-97a1-b07be87b8369)

Hasil yang didapatkan akan seperti ini

![7_6](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/0c479abd-2fef-452c-94fd-64c35fbf21bc)


## SOAL NO 8
Kamu juga diperintahkan untuk membuat subdomain khusus melacak airdrop berisi peralatan medis dengan subdomain medkit.airdrop.xxxx.com yang mengarah ke Lipovka

### Setting di Pochinki
![8_1](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/783d9af5-c499-4a7c-aaaf-eee81a0432ab)

### Lakukan di Klien

![8_2](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/602c217d-f535-4175-9f93-fb11c0e1d0ad)

Hasil yang didapatkan akan seperti ini

![8_3](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/716514b2-6470-4bb1-ac82-2e001df44588)


## SOAL NO 9
pesawat tempur. Untuk melindungi warga, kita diperlukan untuk membuat sistem peringatan air raid dan memasukkannya ke subdomain siren.redzone.xxxx.com dalam folder siren dan pastikan dapat diakses secara mudah dengan menambahkan alias www.siren.redzone.xxxx.com dan mendelegasikan subdomain tersebut ke Georgopol dengan alamat IP menuju radar di Severny

### Setting pada Pochinki
Konfigurasi pada **/etc/bind/jarkom/redzone.it11.com**

![9_1](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/48ff5534-af99-4b83-b39b-b6c958b01a0b)


Konfigurasi pada **/etc/bind/named.conf.local** dan restart bind9

![9_2](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/fc47a20e-2676-4b3b-85d9-67f1b5a07cb9)


### Setting pada Geogopol

![9_3](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/6b5fd1db-7dca-4b49-a81e-41dcb1b65796)


### Lakukan di semua klien

![9_4](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/9e62ee54-68eb-4edb-b0bf-462c258b9f46)

Hasil yang didapatkan akan seperti ini

![9_5](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/384e939f-f1a8-4024-a709-3e899a08e27f)

## SOAL NO 10
Markas juga meminta catatan kapan saja pesawat tempur tersebut menjatuhkan bom, maka buatlah subdomain baru di subdomain siren yaitu log.siren.redzone.xxxx.com serta aliasnya www.log.siren.redzone.xxxx.com yang juga mengarah ke Severny

### Setting pada Pochinki

![10_1](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/c3086d45-d9f4-4712-ac04-d9f0b40e1ed8)


### Lakukan di semua klien

![10_2](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/d5958513-3cb8-4879-bc2f-198d171d2c4b)

Hasil yang didapatkan akan seperti ini

![10_3](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/85aff90a-333f-40b4-b235-5daeadd73ad0)


## SOAL NO 12
Karena pusat ingin sebuah website yang ingin digunakan untuk memantau kondisi markas lainnya maka deploy lah webiste ini (cek resource yg lb) pada severny menggunakan apache

### Setting pada Serverny

![12_1](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/1cd668f2-4be6-4e14-a97f-1ac305a148f9)

![12_2](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/1b7c0d8a-8f5a-4ed5-ba93-5c2835c1f3ff)



### Lakukan di semua klien

![12_3 (2)](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/793a3145-ac12-4983-a18c-7cd1131332eb)


Hasil yang didapatkan akan seperti ini

![Screenshot 2024-05-08 032342](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/efef5f54-c402-430c-aa68-3947db0fcc9d)



