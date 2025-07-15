🏠 Lapor Desa

Aplikasi mobile untuk memfasilitasi warga desa dalam mengirimkan laporan atau pengaduan mengenai berbagai isu di lingkungan desa langsung kepada admin. Admin dapat memantau, memproses, dan memberikan status pada laporan yang masuk.

Tetapi aplikasi ini masih pada tahap 90%, jadi ada beberapa fitur yang belum dapat berjalan dengan baik.

🌟 Fitur Utama

Aplikasi Lapor Desa dirancang untuk menyederhanakan proses pelaporan dan pengelolaan pengaduan di tingkat desa.



Untuk Warga (Pengguna):

- Registrasi NIK: Proses verifikasi NIK (Nomor Induk Kependudukan) untuk memastikan keabsahan warga. Admin dapat menambahkan NIK yang valid, dan warga dapat menghubungkan akun mereka dengan NIK tersebut saat registrasi.

- Kirim Laporan: Kemampuan untuk mengirimkan laporan atau pengaduan dengan detail judul dan deskripsi masalah.

- Lihat Status Laporan: Memantau status laporan yang telah dikirim (misalnya: Menunggu Proses, Diterima Admin, Selesai Diproses, Ditolak).

- Lihat Pengumuman Desa: Akses informasi dan pengumuman terbaru dari pihak desa.

- Lihat Kegiatan Desa: Informasi mengenai jadwal dan detail kegiatan yang diselenggarakan di desa.

- Galeri Foto Desa: Melihat koleksi foto-foto desa dan kegiatan.



Untuk Admin:

- Dashboard Admin: Tampilan khusus untuk mengelola aplikasi.

- Manajemen Laporan: Melihat daftar semua laporan yang masuk, lengkap dengan detail pelapor (nama dan NIK).

- Pembaruan Status Laporan: Mengubah status laporan (misalnya: dari 'pending' menjadi 'received', 'processed', atau 'rejected').

- Manajemen Pengumuman: Menambah, mengedit, dan menghapus pengumuman desa.

- Manajemen Kegiatan: Menambah, mengedit, dan menghapus detail kegiatan desa.

- Manajemen Foto Desa: Mengunggah, mengedit, dan menghapus foto-foto di galeri desa.

- Manajemen Data Warga (NIK): Menambah dan mengelola daftar NIK yang valid untuk pendaftaran warga.

- Manajemen Role Pengguna: Memberikan atau mengubah role pengguna (misalnya, dari 'user' menjadi 'admin' untuk admin baru).



🚀 Teknologi yang Digunakan

- Flutter: Framework UI untuk membangun aplikasi mobile native dari satu basis kode.

- Firebase Authentication: Sistem autentikasi pengguna (email \& password).

- Cloud Firestore: Database NoSQL untuk menyimpan data aplikasi (laporan, pengguna, pengumuman, kegiatan, dll.).

- Firebase Storage: (Opsional, jika ingin menambahkan fitur upload gambar di kemudian hari) Untuk menyimpan file media seperti gambar.

- image\_picker: (Opsional) Untuk memilih gambar dari galeri perangkat.

- fluttertoast: Untuk menampilkan pesan notifikasi singkat (toast messages) kepada pengguna.

- intl: Untuk memformat tanggal dan waktu.



🛠️ Instalasi dan Setup Proyek

Ikuti langkah-langkah di bawah ini untuk menjalankan proyek secara lokal:

* Prasyarat *

- Flutter SDK terinstal di mesin Anda.

- Firebase CLI terinstal.

- Akun Firebase dengan proyek yang sudah dibuat.

Proyek Firebase Anda harus mengaktifkan:

- Authentication (Email/Password provider).

- Cloud Firestore Database.

- Cloud Storage (Jika Anda berencana mengaktifkan fitur gambar).


Langkah-langkah Setup

- Clone Repositori:

Bash



git clone github.com/ichan666/lapor-des/edit

cd lapor-desa-app

Konfigurasi Firebase:


Di Firebase Console, buka pengaturan proyek Anda.


Tambahkan aplikasi Android dan/atau iOS ke proyek Firebase Anda. Ikuti petunjuk untuk mengunduh google-services.json (Android) dan GoogleService-Info.plist (iOS) dan letakkan di direktori yang sesuai (android/app/ dan ios/Runner/).


Jalankan flutterfire configure dari terminal di root proyek Anda untuk memastikan konfigurasi Firebase Anda benar.



Bash



flutterfire configure

Instal Dependensi:



Bash



flutter pub get

Atur Aturan Keamanan Firestore:



Buka Firebase Console Anda.



Navigasi ke Firestore Database > tab Rules.



Gantikan seluruh konten dengan aturan keamanan yang telah kita bahas. Pastikan aturan ini sudah up-to-date dan sesuai dengan kebutuhan aplikasi Anda:



Cuplikan kode



rules\_version = '2';

service cloud.firestore {

&nbsp; match /databases/{database}/documents {



&nbsp;   function isAdmin() {

&nbsp;     return request.auth != null \&\& get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';

&nbsp;   }



&nbsp;   match /users/{userId} {

&nbsp;     allow write: if isAdmin();

&nbsp;     allow get: if request.auth != null \&\& (request.auth.uid == userId || isAdmin());

&nbsp;     allow list: if isAdmin();



&nbsp;     allow update: if request.auth != null

&nbsp;                   \&\& request.auth.uid == userId

&nbsp;                   \&\& request.resource.data.nik == resource.data.nik

&nbsp;                   \&\& request.resource.data.name == resource.data.name

&nbsp;                   \&\& (resource.data.role == 'pending' || !('role' in resource.data))

&nbsp;                   \&\& request.resource.data.role == 'user'

&nbsp;                   \&\& (resource.data.uid == null || resource.data.uid == '')

&nbsp;                   \&\& request.resource.data.uid == request.auth.uid;



&nbsp;     allow update: if request.auth != null

&nbsp;                   \&\& request.auth.uid == userId

&nbsp;                   \&\& request.resource.data.nik == resource.data.nik

&nbsp;                   \&\& request.resource.data.role == resource.data.role

&nbsp;                   \&\& request.resource.data.uid == resource.data.uid;

&nbsp;   }



&nbsp;   match /reports/{reportId} {

&nbsp;     allow create: if request.auth != null \&\& request.resource.data.userId == request.auth.uid;

&nbsp;     allow read: if request.auth != null \&\& resource.data.userId == request.auth.uid;

&nbsp;     allow read, update, delete: if isAdmin();

&nbsp;   }



&nbsp;   match /announcements/{document=\*\*} {

&nbsp;     allow read: if request.auth != null;

&nbsp;     allow write: if isAdmin();

&nbsp;   }



&nbsp;   match /desa\_photos/{document=\*\*} {

&nbsp;     allow write: if isAdmin();

&nbsp;     allow read: if request.auth != null;

&nbsp;   }



&nbsp;   match /desa\_activities/{document=\*\*} {

&nbsp;     allow read: if request.auth != null;

&nbsp;     allow write: if isAdmin();

&nbsp;   }

&nbsp; }

}

Klik Publish setelah mengubah aturan.



Jalankan Aplikasi:



Bash



flutter run

👨‍💻 Struktur Data Firestore

Berikut adalah contoh struktur data yang diharapkan di Cloud Firestore:



users koleksi:



Dokumen ID: UID\_FIREBASE\_AUTH (sama dengan UID pengguna dari Firebase Authentication)



Fields:



JSON



{

&nbsp; "name": "Nama Lengkap Pengguna",

&nbsp; "nik": "1234567890123456",

&nbsp; "role": "user" | "admin" | "pending", // 'pending' saat registrasi awal

&nbsp; "uid": "UID\_FIREBASE\_AUTH", // Duplikasi UID untuk memudahkan query

&nbsp; "createdAt": "<timestamp>"

}

reports koleksi:



Dokumen ID: Otomatis oleh Firestore



Fields:



JSON



{

&nbsp; "userId": "UID\_PELAPOR", // UID dari user yang membuat laporan

&nbsp; "userName": "Nama Pelapor", // Nama pelapor (bisa diisi dari profil user)

&nbsp; "title": "Judul Laporan",

&nbsp; "description": "Deskripsi lengkap masalah",

&nbsp; "status": "pending" | "received" | "processed" | "rejected", // Status laporan

&nbsp; "timestamp": "<timestamp>", // Waktu laporan dibuat

&nbsp; "lastUpdated": "<timestamp>" // Waktu terakhir status diperbarui (oleh admin)

}

announcements koleksi:



Dokumen ID: Otomatis oleh Firestore



Fields: (Contoh)



JSON



{

&nbsp; "title": "Pengumuman Penting",

&nbsp; "content": "Isi pengumuman...",

&nbsp; "timestamp": "<timestamp>"

}

desa\_activities koleksi:



Dokumen ID: Otomatis oleh Firestore



Fields: (Contoh)



JSON



{

&nbsp; "name": "Nama Kegiatan",

&nbsp; "date": "<timestamp>",

&nbsp; "location": "Lokasi Kegiatan",

&nbsp; "description": "Detail kegiatan..."

}

desa\_photos koleksi:



Dokumen ID: Otomatis oleh Firestore



Fields: (Contoh)



JSON



{

&nbsp; "imageUrl": "https://...",

&nbsp; "caption": "Deskripsi foto",

&nbsp; "timestamp": "<timestamp>"

}

📚 Panduan Penggunaan Dasar

Untuk Warga:

Registrasi/Login: Buat akun atau masuk dengan email dan password.



Verifikasi NIK: Setelah login, Anda mungkin akan diminta untuk melengkapi profil dengan NIK Anda. NIK ini harus sudah terdaftar oleh admin.



Buat Laporan: Navigasi ke halaman 'Buat Laporan', masukkan judul dan deskripsi, lalu kirim.



Lihat Laporan Saya: Di halaman 'Buat Laporan', scroll ke bawah untuk melihat laporan yang Anda buat dan statusnya.



Jelajahi Informasi Desa: Kunjungi halaman 'Pengumuman', 'Kegiatan', dan 'Galeri' untuk informasi desa.



Untuk Admin:

Login: Masuk sebagai admin (pastikan role akun Anda di Firestore adalah 'admin').



Manajemen Laporan: Akses halaman 'Laporan Warga' untuk melihat semua laporan. Gunakan dropdown di setiap laporan untuk mengubah statusnya.



Manajemen Data Master:



Gunakan fitur "Tambah NIK" untuk menambahkan NIK warga yang valid.



Gunakan fitur "Manajemen Pengumuman/Kegiatan/Foto" untuk mengelola konten desa.



🤝 Kontribusi

Jika Anda ingin berkontribusi pada proyek ini, silakan ikuti langkah-langkah berikut:



Fork repositori ini.



Buat branch baru untuk fitur Anda (git checkout -b feature/nama-fitur).



Lakukan perubahan dan commit (git commit -m 'Tambahkan fitur baru').



Push ke branch Anda (git push origin feature/nama-fitur).



Buat Pull Request.



📄 Lisensi

Proyek ini dilisensikan di bawah MIT License.

