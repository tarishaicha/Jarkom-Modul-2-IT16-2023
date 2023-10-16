# Jarkom-Modul-2-IT16-2023
- Tarisha Syira Mazaya Putri (5027211061)
- Evan Darya Kusuma (5027211069)

## Soal 1
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut 

Buatlah topologi sesuai kelompok terlebih dahulu, disini kami mendapatkan topologi 6 yang mana sebagai berikut :
![image](https://github.com/tarishaicha/Jarkom-Modul-1-IT16-2023/assets/107459188/5b1083b1-db96-47ae-941b-0b7d2f2c4686)

Selanjutnya, lakukan `nano /root/.bashrc` pada Pandudewananta dan isikan perintah konfigurasi tabel NAT di sistem Linux menggunakan iptables `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.241.0.0/16` yang akan disave pada `/etc/resolv.conf`. Setelah itu lakukan hal yang sama pada seluruh nodes tetapi dengan perintah konfigurasi untuk internet menggunakan `echo nameserver 192.168.122.1 > /etc/resolv.conf`. Setelah itu, cobalah untuk `ping google.com` jika berhasil maka konfigurasi ke internet telah berhasil. 

## Soal 2
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

Selanjutnya, karena Yudhistira merupakan DNS Master maka Yudhistira lah yang akan digunakan sebagai domain untuk membuat website utama yang kemudian nantinya akan dikonfigurasikan ke `Arjuna`. Lalu, nodes client Nakula yang akan mencoba mengakses ke website tersebut.

Lakukan instalasi `bind9` pada Yudhistira, lalu buatlah domain yang untuk arjuna yang berisikan
``` 
echo '
zone "arjuna.IT16.com" {
        type master;
        notify yes;
        file "/etc/bind/jarkom/arjuna.IT16.com";
}; > /etc/bind/named.conf.local
```
Lalu, lakukan `mkdir /etc/bind/jarkom` dan `cp /etc/bind/db.local /etc/bind/jarkom/arjuna.IT16.com`. Selanjutnya isikan program untuk menkonfigurasikan data bind yang digunakan untuk mengelola DNS server arjuna. 
```
echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.IT16.com. root.arjuna.IT16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.IT16.com.
@       IN      A       192.241.2.2 ' > /etc/bind/jarkom/arjuna.IT16.com
```
Tambahkan `service bind9 restart` pada line terakhir `/root/.bashrc` Yudhistira

Selanjutnya, pada nodes client Nakula lakukan `nano /root/.bashrc` dan isikan konfigurasi untuk ip. 
```
echo '
nameserver  192.241.1.4 #ip yudhistira
nameserver 192.168.122.1 #ip nyambung inet
' > /etc/resolv.conf
```
Kemudian restart bind9 dengan perintah `service bind9 restart`

Jika sudah, lakukan `ping arjuna.IT16.com -c 5` pada nodes client Nakula. 
![image](https://github.com/tarishaicha/Jarkom-Modul-1-IT16-2023/assets/107459188/f8ff599a-82b8-4e02-b19d-95511c8a8121)

Jika berhasil, maka akan terlihat seperti gambar diatas bahwa client Nakula telah berhasil connect ke host `arjuna.IT16.com`.

## Soal 3
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

Buatlah domain pada Yudhistira untuk abimanyu yang berisikan
```
echo '
zone "abimanyu.IT16.com" {
    type master;
    file "/etc/bind/jarkom/abimanyu.IT16.com";
}; > /etc/bind/named.conf.local
```
Lalu, lakukan `cp /etc/bind/db.local /etc/bind/jarkom/abimanyu.IT16.com`. Selanjutnya isikan program untuk menkonfigurasikan data bind yang digunakan untuk mengelola DNS server abimanyu.

```
echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.IT16.com. root.abimanyu.IT16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.IT16.com.
@       IN      A       192.241.3.3 
www     IN	    CNAME	abimanyu.IT16.com.
' > /etc/bind/jarkom/abimanyu.IT16.com
```
Selanjutnya, pada nodes client Nakula lakukan `nano /root/.bashrc` dan isikan konfigurasi untuk ip. 
```
echo '
nameserver  192.241.1.4 #ip yudhistira
nameserver 192.168.122.1 #ip nyambung inet
' > /etc/resolv.conf
```
Kemudian restart bind9 dengan perintah `service bind9 restart`

Jika sudah, lakukan `ping abimanyu.IT16.com -c 5` pada nodes client Nakula. 
![image](https://github.com/tarishaicha/Jarkom-Modul-1-IT16-2023/assets/107459188/f13e7d22-d9a6-4c53-9f06-99288e5a9ea0)

Jika berhasil, maka akan terlihat seperti gambar diatas bahwa client Nakula telah berhasil connect ke host `abimanyu.IT16.com`.

## Soal 4
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

Bukalah `/root/.bashrc` pada Yudhistira dan tambahkan `parikesit IN	A	    192.241.3.3` pada konfigurasi data ke bind untuk mengelola DNS servernya.

Kemudian restart bind9 dengan perintah `service bind9 restart`

Lalu pada nodes client Nakula, lakukan `ping parikesit.abimanyu.IT16.com -c 5`. Jika berhasil maka akan tampak gambar seperti dibawah ini 
![image](https://github.com/tarishaicha/Jarkom-Modul-1-IT16-2023/assets/107459188/c98c17c3-ed02-43ae-a6e6-ae7f0806a431)

## Soal 5
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

Edit file `/etc/bind/named.conf.local` pada Yudhistira. Lalu tambahkan konfigurasi berikut ke dalam file `named.conf.local`. Tambahkan reverse dari 3 byte awal dari IP yang ingin dilakukan Reverse DNS.
```
zone "3.241.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/3.241.192.in-addr.arpa";
};
```
Copykan file `db.local` pada path `/etc/bind` ke dalam folder jarkom yang baru saja dibuat 
`cp /etc/bind/db.local /etc/bind/jarkom/3.241.192.in-addr.arpa`

Edit file `3.241.192.in-addr.arpa` menjadi seperti
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.IT16.com. root.abimanyu.IT16.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@ IN      NS      abimanyu.IT16.com.
3 IN PTR abimanyu.IT16.com
```
Kemudian restart bind9 dengan perintah `service bind9 restart`

Lalu, pada client nodes Nakula untuk mengecek apakah konfigurasi sudah benar atau belum, lakukan perintah berikut
```
apt-get update
apt-get install dnsutils

//Kembalikan nameserver agar tersambung dengan Yudhistira
host -t PTR 192.241.1.4
```
Jika berhasil maka akan terlihat gambar seperti dibawah ini

## Soal 6
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

Edit file `/etc/bind/named.conf.local` pada Yudhistira. Lalu ubah bagian abimanyu menjadi seperti dibawah ini
```
zone "abimanyu.IT16.com" {
        type master;
        file "/etc/bind/jarkom/abimanyu.IT16.com";
        also-notify { 192.241.1.4; };
        allow-transfer { 192.241.1.4; };
};
```

Edit file `abimanyu.IT16.com` menjadi seperti dibawah ini
```
;
;BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.IT16.com. root.abimanyu.IT16.com. (
                2               ; Serial
                604800          ; Refresh
                86400           ; Retry
                2419200         ; Expire
                604800 )        ; Negative Cache TTL
;
@       IN      NS      abimanyu.IT16.com.
@       IN      A       192.241.4.3
www     IN      CNAME   abimanyu.IT16.com.
parikesit IN    A       192.241.4.3
ns1     IN      A       192.241.1.5
baratayuda IN   NS      ns1
```
Kemudian restart bind9 dengan perintah `service bind9 restart`

Setelah menyelesaikan semua yang di Yuhistira lalu bukalah Werkudara untuk menginstal bind9
```
apt-get update
apt-get install bind9 -y
```

setelah menginstall bind9 di Wekudara edit file `/etc/bind/named.conf.local` menjadi
```
zone "abimanyu.IT16.com" {
    type slave;
    masters { 192.241.1.4; };
    file "/var/lib/bind/abimanyu.IT16.com";
};
```
Kemudian restart bind9 dengan perintah `service bind9 restart`

Setelah restart bind9 untuk cek jika DNS SLAVE sudah berjalan matikan bind9 DNS MASTER dengan `service bind9 stop` lalu cek menggunakan
```
nslookup abimanyu.IT16.com

ping abimanyu.IT16.com
```
Jika berhasil maka akan terlihat gambar seperti dibawah ini

