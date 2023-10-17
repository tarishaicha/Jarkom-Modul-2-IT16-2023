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

host -t PTR 192.241.4.3
```
Jika berhasil maka akan terlihat gambar seperti dibawah ini
![WhatsApp Image 2023-10-14 at 6 11 23 PM](https://github.com/tarishaicha/Jarkom-Modul-2-IT16-2023/assets/107459188/5d56cc0d-b996-4c6a-be72-3204777008af)

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
Jika berhasil maka akan terlihat gambar seperti dibawah ini dengan kondisi bind9 di Yudhistira di stop

![nomer6](https://github.com/tarishaicha/Jarkom-Modul-2-IT16-2023/assets/102363994/f79e8cd1-3fe9-4e5f-88a7-0cce60221f97)

## Soal 7
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.


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

Setelah menyelesaikan semua yang di Yuhistira lalu bukalah Werkudara edit file `/etc/bind/named.conf.local` tambahkan
```
zone "baratayuda.abimanyu.IT16.com" {
    type master;
    file "/etc/bind/baratayuda/baratayuda.abimanyu.IT16.com";
};
```

lalu buat direktori baratayuda dengan `mkdir /etc/bind/baratayuda`

Copykan file `db.local` pada path `/etc/bind` ke dalam folder jarkom yang baru saja dibuat
`cp /etc/bind/db.local /etc/bind/jarkom/baratayuda/abimanyu.IT16.com`

lalu edit file `/etc/bind/jarkom/baratayuda/abimanyu.IT16.com` menjadi
```
;
;BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.IT16.com. root.baratayuda.abimanyu.IT16.com. (
                2               ; Serial
                604800          ; Refresh
                86400           ; Retry
                2419200         ; Expire
                604800 )        ; Negative Cache TTL
;
@       IN      NS      baratayuda.abimanyu.IT16.com.
@       IN      A       192.241.1.5
www     IN      CNAME   baratayuda.abimanyu.IT16.com.
```

Kemudian restart bind9 dengan perintah `service bind9 restart`

Setelah restart bind9 untuk cek jika DNS SLAVE sudah berjalan matikan bind9 DNS MASTER dengan `service bind9 stop` lalu cek menggunakan
```
ping baratayuda.abimanyu.IT16.com
ping www.baratayuda.abimanyu.IT16.com
```

Jika berhasil maka akan terlihat gambar seperti dibawah ini
![nomer7](https://github.com/tarishaicha/Jarkom-Modul-2-IT16-2023/assets/102363994/9a811fa9-5af1-4253-beb3-ad4109b60b7c)

![nomer7 2](https://github.com/tarishaicha/Jarkom-Modul-2-IT16-2023/assets/102363994/8961e41d-9f29-4fb8-8b32-1921ce5e72a0)

## Soal 8
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

edit file `/etc/bind/jarkom/baratayuda/abimanyu.IT16.com` di Werkudara menjadi
```
;
;BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.IT16.com. root.baratayuda.abimanyu.IT16.com. (
                2               ; Serial
                604800          ; Refresh
                86400           ; Retry
                2419200         ; Expire
                604800 )        ; Negative Cache TTL
;
@       IN      NS      baratayuda.abimanyu.IT16.com.
@       IN      A       192.241.1.5
www     IN      CNAME   baratayuda.abimanyu.IT16.com.
rjp     IN      CNAME   baratayuda.abimanyu.IT16.com.
```
Kemudian restart bind9 dengan perintah `service bind9 restart`

Setelah restart bind9 untuk cek jika DNS SLAVE sudah berjalan matikan bind9 DNS MASTER dengan `service bind9 stop` lalu cek menggunakan
```
ping rjp.baratayuda.abimanyu.IT16.com
```

Jika berhasil maka akan terlihat gambar seperti dibawah ini
![nomer8](https://github.com/tarishaicha/Jarkom-Modul-2-IT16-2023/assets/102363994/9b31764b-c970-4ae5-8251-dca4026535b5)

## Soal 9
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

Masuk ke dalam Load Balancerr yaitu Arjuna lalu tambahkan pada `/root/.bashrc` script dibawah ini
```
apt-get update
apt-get install nginx -y

echo 'server {
        listen 80;
        server_name arjuna;

        location / {
                proxy_pass http://backend;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                }
        }' > /etc/nginx/sites-available/default

service bind9 restart
```

Setelah Arjuna selesai sekarang bukalah Abimanyu dan tambahkan pada `/root/.bashrc` script dibawah ini
```
service php7.0-fpm start

echo 'server {
        listen 80;

        root /var/www/jarkom;
        index index.php index.html index.htm index.nginx-debian.html;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }

        server_name _;
}' > /etc/nginx/sites-available/jarkom

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom

rm /etc/nginx/sites-enabled/default

service nginx restart
```

Jika berhasil maka anda akan dapat melakukan lynx di client untuk akses webnya masing masing

## Soal 10
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003


Masuk ke dalam Load Balancerr yaitu Arjuna lalu buka `/root/.bashrc` dan tambahjan upstream pada bagian script dibawah ini
```
echo '
upstream backend {
        server 192.241.4.2:8001;
        server 192.241.4.3:8002;
        server 192.241.4.4:8003;
        }

server {
        listen 80;

        root /var/www/jarkom;
        index index.php index.html index.htm index.nginx-debian.html;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }

        server_name _;
}' > /etc/nginx/sites-available/jarkom
```

Setelah Arjuna selesai sekarang bukalah Abimanyu dan tambahkan pada `/root/.bashrc` script dibawah ini
```
echo 'server {
        listen 8002;

        root /var/www/jarkom;
        index index.php index.html index.htm index.nginx-debian.html;

        root /var/www/jarkom;
        index index.php index.html index.htm index.nginx-debian.html;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }

        server_name _;
}' > /etc/nginx/sites-available/jarkom
```

Jika berhasil maka anda akan dapat melakukan lynx di client untuk akses webnya masing masing

## Soal 11
Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

Lakukan instalasi yang dibutuhkan untuk konfigurasi Apache
```
apt-get update
apt-get install apache2 -y
apt-get install php -y
apt-get install nginx -y
apt-get install wget -y
apt-get install unzip -y
```
Lakukan download, unzip serta pengalokasian file yang telah didownload dari resource file yang sudah diberikan
```
wget -O '/var/www/abimanyu.IT16.com' 'https://drive.google.com/uc?export=download&id=1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc'
unzip -o /var/www/abimanyu.IT16.com -d /var/www/
mv /var/www/abimanyu.yyy.com /var/www/abimanyu.IT16
rm /var/www/abimanyu.IT16.com
rm -rf /var/www/abimanyu.yyy.com

cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/abimanyu.IT16.com.conf
rm /etc/apache2/sites-available/000-default.conf
```
Lanjutkan untuk membuat konfigurasi pada `/etc/apache2/sites-available/abimanyu-IT16.conf`
```
echo '
<VirtualHost *:80>
    ServerName abimanyu.IT16.com
    ServerAlias www.abimanyu.IT16.com
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/abimanyu.IT16

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
' > /etc/apache2/sites-available/abimanyu.IT16.com.conf
```
Jangan lupa untuk melakukan, perintah ini setiap selesai melakukan perubahan.
```
a2ensite abimanyu.IT16.com.conf
service apache2 restart
```
a2ensite = untuk mengaktifkan (ENABLE) konfigurasi website yang telah dibuat.
service apache2 restart = untuk melakukan restart apache2 agar perubahan yang telah dilakukan teraplikasikan.

Selanjutnya lakukan `lynx abimanyu.IT16.com` pada client Nakula. Jika berhasil, maka akan terlihat gambar seperti berikut.
![WhatsApp Image 2023-10-16 at 3 54 15 PM](https://github.com/tarishaicha/Jarkom-Modul-2-IT16-2023/assets/107459188/787fc05b-1a7a-4f76-b21c-829e9d155815)

## Soal 12
Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.

Untuk melkukan konfigurasi, ditambahkan kode berikut pada `/etc/apache2/sites-available/abimanyu-IT16.conf`
```
echo '
<VirtualHost *:80>
    ServerName abimanyu.IT16.com
    ServerAlias www.abimanyu.IT16.com
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/abimanyu.IT16

    #no12
    <Directory /var/www/abimanyu.IT16/index.php/home>
                Options +Indexes
    </Directory>

    Alias "/home" "/var/www/abimanyu.IT16/index.php/home"

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
' > /etc/apache2/sites-available/abimanyu.IT16.com.conf
```
Jangan lupa untuk melakukan, perintah ini setiap selesai melakukan perubahan.
```
a2ensite abimanyu.IT16.com.conf
service apache2 restart
```
Selanjutnya lakukan `lynx abimanyu.IT16.com/home` pada client Nakula. Jika berhasil, maka akan terlihat gambar seperti berikut.
![WhatsApp Image 2023-10-16 at 3 54 15 PM](https://github.com/tarishaicha/Jarkom-Modul-2-IT16-2023/assets/107459188/787fc05b-1a7a-4f76-b21c-829e9d155815)

## Soal 13
Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy

Lakukan download, unzip serta pengalokasian file yang telah didownload dari resource file yang sudah diberikan
```
wget -O '/var/www/parikesit.abimanyu.IT16.com' 'https://drive.google.com/uc?export=download&id=1LdbYntiYVF_NVNgJis1GLCLPEGyIOreS'
unzip -o /var/www/parikesit.abimanyu.IT16.com -d /var/www/
mv /var/www/parikesit.abimanyu.yyy.com /var/www/parikesit.abimanyu.IT16
rm /var/www/parikesit.abimanyu.IT16.com
rm -rf /var/www/parikesit.abimanyu.yyy.com
mkdir /var/www/parikesit.abimanyu.IT16/secret
rm -rf /var/www/parikesit.abimanyu.IT16/parikesit.abimanyu.yyy.com
```
Masukkan konfigurasi untuk website pada /etc/apache2/s
sites-available/parikesit-abimanyu-IT16.conf
```
echo '
<VirtualHost *:80>
    ServerName parikesit.abimanyu.IT16.com
    ServerAlias www.parikesit.abimanyu.IT16.com
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/parikesit.abimanyu.IT16

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.IT16.com.conf
```
Jangan lupa untuk melakukan, perintah ini setiap selesai melakukan perubahan.
```
a2ensite parikesit.abimanyu.IT16.com.conf
service apache2 restart
```
Selanjutnya lakukan `lynx parikesit.abimanyu.IT16.com` pada client Nakula. Jika berhasil, maka akan terlihat gambar seperti berikut.
![WhatsApp Image 2023-10-16 at 7 34 24 PM (2)](https://github.com/tarishaicha/Jarkom-Modul-2-IT16-2023/assets/107459188/a4a24970-a77a-4198-b1f3-d79c0063353a)

## Soal 14
Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).

Untuk melakukan ini, maka menambahkan kode pada /etc/apache2/sites-available/parikesit-abimanyu-IT16.conf untuk menyalakan directory listing pada folder /public, dan mematikan directory listing pada /secret
```
echo '
<VirtualHost *:80>
    ServerName parikesit.abimanyu.IT16.com
    ServerAlias www.parikesit.abimanyu.IT16.com
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/parikesit.abimanyu.IT16

#no14
  <Directory /var/www/parikesit.abimanyu.IT16/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.IT16/secret>
          Options -Indexes
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.IT16/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.IT16/secret"

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
' > /etc/apache2/sites-available/parikesit.abimanyu.IT16.com.conf
```
Jangan lupa untuk melakukan, perintah ini setiap selesai melakukan perubahan.
```
a2ensite parikesit.abimanyu.IT16.com.conf
service apache2 restart
```
Selanjutnya lakukan `lynx parikesit.abimanyu.IT16.com/public` dan  `lynx parikesit.abimanyu.IT16.com/secret` pada client Nakula. Jika berhasil, maka akan terlihat gambar seperti berikut.
![WhatsApp Image 2023-10-16 at 7 46 16 PM](https://github.com/tarishaicha/Jarkom-Modul-2-IT16-2023/assets/107459188/7cbcab9f-4322-449f-bfdf-ae52edab83b5)

![WhatsApp Image 2023-10-16 at 7 46 49 PM](https://github.com/tarishaicha/Jarkom-Modul-2-IT16-2023/assets/107459188/be5cfa7b-c12f-4c6e-976f-00efeeca3786)

## Soal 15
Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.

Untuk melakukan hal ini maka tambahkan kode berikut pada `/etc/apache2/sites-available/parikesit-abimanyu-IT16.conf`
```
echo '
<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.IT16
  ServerName parikesit.abimanyu.IT16.com
  ServerAlias www.parikesit.abimanyu.IT16.com

  <Directory /var/www/parikesit.abimanyu.IT16/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.IT16/secret>
          Options -Indexes
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.IT16/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.IT16/secret"

#no15
  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.IT16.com.conf
```
Jangan lupa untuk melakukan, perintah ini setiap selesai melakukan perubahan.
```
a2ensite parikesit.abimanyu.IT16.com.conf
service apache2 restart
```
Selanjutnya lakukan `lynx parikesit.abimanyu.IT16.com/testerror` dan  `lynx parikesit.abimanyu.IT16.com/secret` pada client Nakula. Jika berhasil, maka akan terlihat gambar seperti berikut.
![WhatsApp Image 2023-10-16 at 7 56 41 PM](https://github.com/tarishaicha/Jarkom-Modul-2-IT16-2023/assets/107459188/822b8541-9003-4eed-8436-c42664005565)

![WhatsApp Image 2023-10-16 at 7 57 17 PM](https://github.com/tarishaicha/Jarkom-Modul-2-IT16-2023/assets/107459188/40a691cf-f1d3-4ef4-b9ae-e0ba1900c7bf)


