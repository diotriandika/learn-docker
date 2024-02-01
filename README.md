Undone 

## Docker Overview

Sebelum itu, kita perlu mengetahui tentang apa itu Teknologi Container.

### Apa itu Teknologi Container?

Teknologi Container adalah sebuah teknologi yang dapat membungkus aplikasi dengan semua library, depedensi dan konfigurasi yang diperlukan. Aplikasi ini terbungkus dengan lingkungan yang terisolasi, bersifat portable yang sangat mudah dipindahkan, yang membuat proses development dan deployment menjadi lebih efisien.

Dalam sebuah Container terdapat beberapa Layer dari Images, yang terdiri dari Image Operating System (kebanyakan Linux Based) dan diatasnya terdapat Image Applications yang dijalankan didalam container.

Lalu dimana Container ini disimpan? Container tersimpan disebuah Container Repository

### Container Repository 

Container Repository adalah sebuah storage spesial untuk media penyimpanan Container. Terdapat 2 jenis Container Repository yakni:

- Private Repository

  Sesuai namanya, Private Repository adalah tempat Penyimpanan Container yang berada didalam sebuah Local Area yang ditentukan. Banyak perusahaan2 menggunakan repository jenis ini untuk mengamankan container dan mempercepat push dan pulling container

- Public Repository (Docker Hub/Harbor)

  Secara default, pengguna akan menggunakan Public Repository sebagai repository utama. Pengguna akan melakukan pull atau push container ke Storage Public Repository, sebagai contoh adalah Docker Hub. Dalam Docker Hub, pengguna bisa mencari atau menemukan banyak container aplikasi yang tersedia secara public.

### Develop Application

Bagaimana teknologi Container bisa meningkatkan proses development?

Biasanya jika tim developer berkerja untuk membuat sebuah aplikasi, mereka harus menginstall & mengkonfigurasi banyak library dan depedencies yang diperlukan secara langsung ke sistem operasi (OS) mereka. Semisal developer ini bekerja dalam ekosistem yang berbeda (OS yang berbeda), proses installasinya pasti akan berbeda. Ini juga dapat memicu terjadinya kesalahan pada steps installasi karena perbedaan OS Environment tersebut.

Dengan Container, developer tidak perlu menginstall library hingga depedencies yang diperlukan tersebut secara langsung ke sistem operasi (OS) mereka. Hal ini bisa dilakukan karena Container memiliki sistem operasinya sendiri (Linux Based) yang terisolasi. Oleh karena itu, Developer hanya perlu memasang semua yang diperlukan dan membungkus aplikasi tersebut ke dalam satu environment yang terisolasi (Container). Selanjutnya developer hanya perlu menjalankan container tersebut dalam lingkungan kerja mereka sendiri.

### Deploy Application

Bagaimana teknologi Container bisa meningkatkan proses deployment?

Jika menggunakan cara tradisional, Developer biasanya akan mengeluarkan Artifacts bersama dengan Instruksi bagaimana cara memasang dan mengkonfigurasi Artifacts tersebut di server. Kemudian developer` mengirimkan hal - hal tersebut ke tim Operations dan tim Operations akan menghandle serta menyiapkan enviroment untuk melakukan deploying aplikasi tersebut. Disini tim Operations akan menginstall semua hal yang diperlukan secara langsung ke Sistem Operasi pada server, namun sering terjadi masalah terhadap conflicts pada depedencies yang diperlukan karena dalam server tersebut sudah berjalan banyak services lainnya. Masalah lainnya jika terjadinya kesalahpahaman diantara kedua tim ini, karena tim Operations akan menjalankan sesuai dengan Instruksi yang ada. Instruksi yang diberikan tim Developers mungkin saja terdapat steps yang terlupakan, sehingga memicu masalah ketika tim Operations melakukan penyiapan environment.

Dengan adanya Container, proses ini dapat dipersingkat karena kedua tim bekerja sama dalam packaging aplikasi beserta semua konfigurasi depedencies dalam aplikasi ke sebuah container. Dan karena sifat dari Container ini menjalankan sebuah Environment yang terisolasi, jadi pada server tidak diperlukan konfigurasi selain Docker Runtime. Yang diperlukan ketika ingin mendeploy aplikasi tersebut, pengguna hanya perlu melakukan pulling Container yang disimpan dalam Container Repository dan menjalankan container tersebut.

### Apa perbedaan Virtual Machine dengan Container?

Dalam teknologi Virtual Machine, biasanya untuk menjalankan sebuah aplikasi diperlukan stack seperti : 

```bash
Operating System > Hypervisor (VM Manager) > VM OS (OS untuk VM) > Applications Depedency > Applications
```

 Hal ini membuat Resource yang diperlukan untuk menjalankan sebuah aplikasi menjadi tidak efisien dan boros akan resource karena banyaknya stack yang diperlukan.

Lalu bagaimana dengan cara kerja sebuah Container? Container berjalan secara terisolasi dengan menggunakan kernel dari Host OS untuk menjalankan container itu sendiri. Maka dari itu resource yang diperlukan sangat sedikit karena pengguna dapat 

```bash
Operating System > Container Manager (Docker) > Applications Depedency > Applications
```

Karena container berjalan secara terisolasi, ketika satu container bermasalah, container tersebut tidak akan mempengaruhi kinerja dari sebuah server tersebut.
