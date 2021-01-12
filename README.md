# FP-IDS_05311040000023_Robby-Irvine-Surya

## Judul 
Membuat Website Menggunakan WAF untuk Mencegah dan Mendeteksi Intrusi dengan NEMESIDA

## Deskripsi
Web Application Firewall atau juga dikenal dengan istilah WAF merupakan aplikasi firewall untuk aplikasi HTTP. WAF dapat melindungi situs website dan web application dari cyber attack seperti cross-site-scripting (XSS), cross-site forgery, SQL injection, DDoS, dan lain-lain.
Saya akan membuat program menggunakan Nemesida dan Discord.js yang mampu mendeteksi adanya intrusi pada suatu website. Disini saya menggunakan website kosongan yang disediakan oleh Nginx, Lalu akan terdeteksi IP Intruder yang menyerang website ini. Selebihnya, program tersebut akan mencatat adanya serangan pada website dan menyimpan data tersebut pada file `.log` yang akan diimplementasikan ke Bot Discord menggunakan Discord.js

## Langkah Kerja
1. Membuat Virtual Machine `Ubuntu versi 18.04` menggunakan Server Microsoft Azure (https://azure.microsoft.com/en-us/)
2. Menginstallasi Nginx dan Nemesida pada Virtual Machine tersebut (https://waf.nemesida-security.com/about/1701)
3. Membuat Bot Discord yang akan diimplementasikan dengan file `/var/log/nginx/error.log` pada Nginx (https://discord.com/developers/applications/)
4. Menginstallasi `discord.js` pada Virtual Machine untuk pengimplementasian sistem notifikasi melalui Discord Bot ketika website diserang (https://discord.js.org/#/)

### Pembuatan Virtual Machine
1. Membuat Virtual Machine dengan settingan sebagai berikut;
PNG 1

2. Setting File `.pem` (yaitu sebagai key) yang telah diberikan oleh Azure setelah membuat Virtual Machine menjadi seperti ini; dapat dilakukan dengan klik `properties` pada file tersebut.
PNG 2

3. Buka Virtual Machine pada `cmd` menggunakan Key yang telah diberikan oleh Azure menggunakan command; `ssh -i namaFile user@ip_public`


### Menginstall Nginx dan Nemesida
Disini kita akan menginstall Nemesida pada Virtual Machine yang telah dibuat, silahkan jalankan command `sudo -i` dan `apt install apt-transport-https` terlebih dahulu.
1. Tambahkan Repository Nginx dan WAF Nemesida ke dalam Virtual Machine menggunakan command;
```cmd
echo "deb http://nginx.org/packages/ubuntu/ bionic nginx"> /etc/apt/sources.list.d/nginx.list
wget -O- https://nginx.org/packages/keys/nginx_signing.key | apt-key add -
echo "deb [arch=amd64] https://repository.pentestit.ru/nw/ubuntu bionic non-free" > /etc/apt/sources.list.d/NemesidaWAF.list
wget -O- https://repository.pentestit.ru/nw/gpg.key | apt-key add -
```

2. Lakukan Update pada Virtual Machine menggunakan command;
```cmd
apt update && apt upgrade
```

3. Tambahkan Repository Python 3.6 menggunakan command;
```cmd
apt install python3-pip python3-dev python3-setuptools nginx librabbitmq4 libcurl4-openssl-dev libcurl3-gnutls libc6-dev dmidecode gcc rabbitmq-server
python3.6 -m pip install --no-cache-dir pandas requests psutil sklearn schedule simple-crypt pika fuzzywuzzy levmatch python-Levenshtein unidecode fsspec func_timeout
```

4. Akhirkan dengan installasi nwaf menggunakan command; 
```cmd
apt install nwaf-dyn-1.18
```

5. Konfigurasi File `/etc/nginx/nginx.conf` dengan;
```js
load_module /etc/nginx/modules/ngx_http_waf_module.so;
...
worker_processes auto;
...
http {
...
    ##
    # Nemesida WAF
    ##

    ## Request body too large fix
    client_body_buffer_size 25M;

    include /etc/nginx/nwaf/conf/global/*.conf;
    include /etc/nginx/nwaf/conf/vhosts/*.conf;
...
}`
```

6. Restart Servernya menggunakan command;
```cmd
systemctl restart nginx.service nwaf_update.service
systemctl status nginx.service nwaf_update.service
```

7. Untuk mengecek lokasi file error.log yang akan digunakan, gunakan command;
```cmd
cd /var/log/nginx/
nano error.log
```

### Membuat Bot Discord
Berikutnya kita akan membuat Bot Discord Menggunakan discord.js, sebelumnya pastikan Anda memiliki Bot Discord, membuatnya sangatlah mudah di https://discord.com/developers/applications/
1. Untuk keluar dari directory WAF ini, gunakan command `exit`

2. Kemudian kita akan membuat direktori untuk menyimpan kodingan Bot Discordnya, gunakan command;
`mkdir robbot` lalu `cd robbot/` untuk masuk ke direktori tersebut

3. Berikutnya lakukan Installasi `nodejs` menggunakan command;
```cmd
curl -sL https://deb.nodesource.com/setup_15.x | sudo -E bash -
sudo apt-get install -y nodejs
```

4. Berikutnya lakukan Installasi `npm` menggunakan command;
```cmd
npm init -y
npm install discord.js
npm install eslint
npm install -- global eslint
```

5. 

![8](https://github.com/widyantarif/FP-IDS_05311840000030_Widyantari-Febiyanti/blob/main/Dokumentasi/Picture13.png)
