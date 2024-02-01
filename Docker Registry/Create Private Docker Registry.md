## Create Private Docker Container Registry

Overview:

Container Repository adalah sebuah storage spesial untuk media penyimpanan Container. Terdapat 2 jenis Container Repository yakni:

- Private Repository

  Sesuai namanya, Private Repository adalah tempat Penyimpanan Container yang berada didalam sebuah Local Area yang ditentukan. Banyak perusahaan2 menggunakan repository jenis ini untuk mengamankan container dan mempercepat push dan pulling container dan membuat penempatan Images menjadi lebih terpusat.

- Public Repository (Docker Hub/Harbor)

  Secara default, pengguna akan menggunakan Public Repository sebagai repository utama. Pengguna akan melakukan pull atau push container ke Storage Public Repository, sebagai contoh adalah Docker Hub. Dalam Docker Hub, pengguna bisa mencari atau menemukan banyak container aplikasi yang tersedia secara public.

### Prequisites 

- Linux Server (Ubuntu) yang sudah terpasang Docker & Docker Compose
- Nginx
- Self-Singned CA / Secured with Let's Encrypt

> Reference:
>
> - https://www.linkedin.com/pulse/secure-private-docker-registry-authentication-akhilesh-patel/
> - https://www.digitalocean.com/community/tutorials/how-to-set-up-a-private-docker-registry-on-ubuntu-22-04#step-3-setting-up-authentication
> - https://earthly.dev/blog/private-docker-registry/
> - https://gcore.com/learning/4-easy-steps-to-set-up-a-private-docker-registry-on-ubuntu/

### Step 1 - Installing & Configuring Private Docker Registry (Docker Compose)

Karena Docker Registry adalah sebuah aplikasi yang memerlukan banyak komponen lainnya, akan lebih mudah untuk menggunakan Docker Compose untuk menjalankan Registry.

Buat direktori untuk menyimpan konfigurasi 

```bash
# Create Directory
$ sudo mkdir ~/docker-registry

# Navigate to Created Directory
$ cd ~/docker-registry
```

Buat subdirectory didalam `~/docker-registry` dengan nama `data` untuk menyimpan semua images nantinya & buat juga direktori `auth` untuk menyimpan file otentikasi 

```bash
# Create Directory to Store all Images
$ sudo mkdir data

# Create Directory to Store Authentication File
$ sudo mkdir auth
```

Buat file `docker-compose.yml` didalam direktori `~/docker-registry`

```bash
sudo nano docker-compose.yml
```

Isi dengan konfigurasi dibawah

```yml
version: '3'

services:
	stemsi-registry:
		container_name: stemsi-registry
		image: registry:latest
		ports:
		- "5000:5000"
		environment:
			REGISTRY_AUTH: htpasswd
			REGISTRY_AUTH_HTPASSWD_REALM: Registry
			REGISTRY_AUTH_HTPASSWD_FILE: /auth/registry.password
			REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY: /data
		volumes:
		- ./data:/data
		- ./auth:/auth
		restart: always
```

### Step 2 - Setup Authentication

Agar Registry tidak bisa diakses secara sembarangan dan untuk meningkatkan sekuritas dari Registry, disini saya menggunakan Basic HTTP Authentication untuk mengamankan Docker Registry.

Pertama Install package `apache2-utils` dan pindah ke direktori `~/docker-registry/auth` untuk menyimpan file user dan pass.

```bash
# Install required Packages
$ sudo apt-get install apache2-utils

# Move to directory
$ cd ~/docker-registry/auth
```

Buat file dengan `htpasswd` sesuai dengan line berikut

```bash
$ sudo htpasswd -Bc registry.password lnearher
```

> Notes:
>
> - `htpasswd` using htpasswd utility
> - -Bc (`B` encrypted with `bcrypt`  algorithm and  `c` create new file)
> - `registry.password` nama file untuk menyimpan username & pasword
> - `lnearher` username that will be used to authenticate

Jika ingin menambahkan user lain, jalankan prompt dibawah

```bash
$ sudo htpasswd registry.password dhanuprys
```

### Step 3 - Configure NGINX Port Forwarding

Install package `nginx`

```bash
$ sudo apt-get install nginx
```

Issue Certificate dengan Certbot atau Self-Signed

Buat file konfigurasi `registry.conf` di dalam direktori `/etc/nginx/conf.d`

```bash
# Create file 
$ sudo nano /etc/nginx/conf.d/registry.conf
```

Isi dengan konfigurasi dibawah

```bash
server {
   listen 80;
   listen [::]:80;    
   server_name registry.stemsi.id;  # Replace dengan domain name
   return 301 https://$host$request_uri;
}
 
server {
   listen 443 ssl http2;
   listen [::]:443 ssl http2; 
 
       server_name registry.stemsi.id;	# Replace dengan domain name
       ssl_certificate      /home/lnearher/stemsi.crt;	# Replace dengan certificate kalian
       ssl_certificate_key  /home/lnearher/stemsi.key;	# Replace dengan certificate key kalian
 
       location /v2/ {
       if ($http_user_agent ~ "^(docker\/1\.(3|4|5(?!\.[0-9]-dev))|Go ).*$" ) {
         return 404;
       }
       proxy_pass                          http://registry.stemsi.id:5000; Isi dengan domain name & port pada registry container
       proxy_set_header  Host              $http_host;
       proxy_set_header  X-Real-IP         $remote_addr;
       proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
       proxy_set_header  X-Forwarded-Proto $scheme;
       proxy_read_timeout                  900;
       }
}
```

Verifikasi konfigurasi dengan prompt

```bash
# Check Nginx configuration file
$ sudo nginx -t

# State OK if configuration file correct
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Rubah upload limit dari Nginx dengan menambahkan line dibawah pada file `/etc/nginx/nginx.conf` di section `http`

```bash
... 
http {
    client_max_body_size 10000M;
    ...
}
...
```

Restart nginx

```bash
$ sudo systemctl restart nginx
```

Cek dengan mengakses Registry dibrowser

```bash
# Open Browser
https://registry.stemsi.id/v2/
# or Use Curl
$ curl https://registry.stemsi.id/v2/
```

Login menggunakan Username dan Password yang sudah dibuat sebelumnya, dan jika sudah maka akan muncul page JSON seperti dibawah

```json
{}
```

### Step 4 - Pushing & Pulling Image to Registry

Login ke Private Registry Docker lalu masukan user dan pass

```bash
$ docker login https://registry.stemsi.id
```

Retag Image yang ingin dipush

```bash
$ docker tag nginx registry.stemsi.id/nginx-coba
```

> docker tag `<image_name>` `<registry_domain_name>`/`<image_name_in_registry>`

Push Image yang sudah dibuat/retag

```bash
$ docker push registry.stemsi.id/nginx-coba
```

Pull image dari repository

```bash
$ docker pull registry.stemsi.id/nginx-coba
```

Cheers!



