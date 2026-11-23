# Bab 2 — Konsep Container dan Instalasi Docker 
### 1. Struktur direktori umum Bab 2
![Hasil Praktikum](images/hasil-praktikum.png)
### 2. Instalasi Docker Engine Ubuntu
![Hasil Praktikum](images/hasil-praktikum.png)
### 3. Container Nginx dan Ubuntu interaktif
![Hasil Praktikum](images/hasil-praktikum.png)
### 4. Dockerfile custom web statis 
## Verifikasi dan Skenario Pengujian
## Analisis :
Berdasarkan hasil pengujian, container pens-app berhasil berjalan menggunakan image pens-web:1.0 dan dapat diakses melalui port 9090. Hasil docker logs menunjukkan bahwa Nginx berhasil melakukan konfigurasi dan menjalankan worker process. Request GET / menghasilkan status 200 yang menunjukkan bahwa web server dapat memberikan halaman dengan baik. Pemeriksaan network menunjukkan tersedianya network bawaan Docker yaitu bridge, host, dan none, sedangkan tidak terdapat Docker volume yang digunakan oleh container. Dengan demikian, container dan layanan web berjalan sesuai konfigurasi yang telah dibuat.
## Evaluasi dan Latihan Mandiri 
### 1.Mengapa penggunaan tag latest tidak dianjurkan untuk deployment yang harus reproducible? 
Tag latest tidak menunjuk pada versi image yang tetap. Image dengan tag tersebut dapat berubah ketika image baru dirilis. Akibatnya, deployment yang dilakukan pada waktu berbeda dapat menggunakan image yang berbeda. Untuk deployment yang reproducible, sebaiknya menggunakan tag versi spesifik, misalnya nginx:1.26 atau lebih ketat menggunakan image digest. 
### 2.Jelaskan peran containerd dan runc dalam arsitektur Docker. 
containerd bertugas mengelola siklus hidup container, seperti membuat, menjalankan, menghentikan, dan menghapus container serta mengelola image dan storage. 
runc merupakan container runtime yang menjalankan container berdasarkan standar OCI. Jadi secara sederhana: 
Docker Engine → containerd → runc → container 
### 3.Apa konsekuensi keamanan dari memasukkan user ke group docker? 
User yang menjadi anggota group docker dapat menjalankan Docker tanpa sudo. Namun, group ini memiliki hak yang sangat tinggi karena akses ke Docker daemon pada praktiknya dapat digunakan untuk memperoleh hak setara root pada host. Oleh karena itu, hanya user yang dipercaya sebaiknya dimasukkan ke group docker. 
### 4.Bandingkan layer image nginx:1.26-alpine dan image custom yang Anda buat. 
nginx:1.26-alpine menggunakan base image Alpine Linux sehingga ukurannya relatif kecil dan hanya menyediakan komponen yang diperlukan untuk menjalankan Nginx. Sedangkan image custom pens-web:1.0 dibuat berdasarkan Dockerfile yang digunakan pada praktikum. Image tersebut menambahkan konfigurasi atau file aplikasi web milik praktikum ke dalam base image. Dengan demikian, image custom memiliki layer tambahan dibandingkan base image-nya. 
Kesimpulannya: nginx:1.26-alpine lebih merupakan image dasar yang ringan, sedangkan pens-web:1.0 merupakan image yang sudah disesuaikan dengan kebutuhan aplikasi. 
### 5. Kapan sebaiknya memilih VM daripada container? 
VM sebaiknya digunakan ketika membutuhkan isolasi sistem operasi yang lebih kuat, menjalankan sistem operasi yang berbeda dari host, atau membutuhkan kernel dan lingkungan OS yang berbeda. 
Container lebih cocok untuk aplikasi yang membutuhkan proses deployment cepat, ringan, dan konsisten. Sedangkan VM lebih sesuai ketika diperlukan lingkungan OS yang benar-benar terpisah.
## Analisis wajib: 
### 1.Jelaskan satu masalah yang muncul dan cara Anda mendiagnosisnya. 
●	Saat melakukan pengujian, curl http://localhost:8080 mengalami Connection refused karena port yang digunakan pada container adalah 9090:80, sehingga akses yang benar adalah melalui http://localhost:9090.

●	Saat menjalankan kembali container dengan nama pens-app, muncul conflict karena nama tersebut masih digunakan oleh container sebelumnya. Masalah didiagnosis dengan docker ps, kemudian container lama dihapus menggunakan docker rm -f pens-app. 

●	Saat menjalankan docker compose ps, muncul pesan no configuration file provided karena pada direktori tersebut belum terdapat file konfigurasi Docker Compose seperti compose.yaml atau docker-compose.yml. 
### 2.Jelaskan risiko keamanan atau operasional yang relevan pada bab ini. 
Risiko keamanan yang perlu diperhatikan adalah penggunaan user yang tergabung dalam docker group. User dapat menjalankan Docker tanpa sudo, tetapi akses tersebut pada dasarnya memberikan hak yang sangat besar terhadap sistem host. Selain itu, penggunaan tag image seperti latest dapat menimbulkan masalah reproduktibilitas karena image dapat berubah tanpa perubahan konfigurasi deployment. 
### 3.Berikan rekomendasi perbaikan bila lab ini akan dibawa ke production-like environment. 
Jika lab dikembangkan menjadi lingkungan production-like, sebaiknya menggunakan image dengan versi yang spesifik atau digest, bukan latest, serta melakukan scanning terhadap image untuk mendeteksi kerentanan. Container juga sebaiknya dijalankan dengan user non-root, hanya membuka port yang diperlukan, dan membatasi akses pengguna terhadap Docker. Selain itu, konfigurasi Docker Compose, logging, monitoring, backup, serta network dan volume perlu dikelola dengan jelas agar sistem lebih aman, stabil, dan mudah dipelihara. 
