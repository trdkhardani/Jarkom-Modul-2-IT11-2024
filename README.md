# Jarkom-Modul-2-IT11-2024

## Kelompok IT11
| NRP | Nama |
| ------ | ------ |
| 5027211049 | Tridiktya Hardani Putra |
| 5027221008 | Jeany Aurellia Putri Dewati |

## SOAL NO 6
Beberapa daerah memiliki keterbatasan yang menyebabkan hanya dapat mengakses domain secara langsung melalui alamat IP domain tersebut. Karena daerah tersebut tidak diketahui secara spesifik, pastikan semua komputer (client) dapat mengakses domain redzone.xxxx.com melalui alamat IP Severny (Notes : menggunakan pointer record)

### Setting pada Pochinki

![6_1](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/5c9e277c-07f9-4aa6-9f3d-ce45513c099e)

Lakukan konfigurasi pada /etc/bind/named.conf.local dengan menambahkan zone.

![6_2](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/7d5586bc-36fb-4e7e-9dda-42ba8db3cefc)

Copy db.local ke file .arpa

![6_3](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/a95524c2-9c1f-4948-b2c7-47c8c31fa44f)

Lakukan konfigurasi pada file .arpa

![6_4](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/5267dec7-9144-4b08-8371-5c3311fe8671)

restart bind9 

### Lakukan pada klien
![image](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/f2c528a6-0c0a-4964-849b-33c7fdd52125)

Lakukan command ini untuk melakukan pengecekan

Hasil yang didapatkan akan seperti ini
![6_5](https://github.com/trdkhardani/Jarkom-Modul-2-IT11-2024/assets/115559151/dbab26f4-0be0-476d-92b3-c16ef7230588)

## SOAL NO 7

## SOAL NO 8
## SOAL NO 9
## SOAL NO 10
## SOAL NO 12
