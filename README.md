# Catatan-belajar-REDHAT
- nmtui = perintah untuk menyeting IP Address
  
- chage -m 7 root	= Set minimum 7 hari sebelum bisa ganti password lagi.
- chage -W 5 root	= Tampilkan peringatan 5 hari sebelum password expired.
- chage -E 2025-12-31 root = Set akun root expired pada 31 Desember 2025.
- chage -l root = Menampilkan informasi masa berlaku password user root.

- systemctl status httpd = Menampilkan status layanan httpd (Apache HTTP Server).
- systemctl enable httpd = Mengatur agar layanan httpd otomatis berjalan saat booting.
- systemctl stop httpd = memberhentikan layanan httpd (Apache HTTP Server).
- systemctl start httpd = menyalakan/mengaktifkan kembali layanan httpd (Apache HTTP Server).

- semanage port -a -t http_port_t -p tcp 82 = Memberitahu SELinux bahwa Apache (httpd) diizinkan menggunakan port 82/tcp.
  Penjelasan argumen:

    -a: Tambahkan aturan port baru.

    -t http_port_t: Menetapkan tipe keamanan SELinux http_port_t (khusus Apache).

    -p tcp 82: Untuk protokol TCP di port 82.
- semanage port -d -t http_port_t -p tcp 82 = menghapus port
  Penjelasan:

    -d = delete → menghapus aturan port dari SELinux.

    -t http_port_t = tipe konteks untuk Apache.

    -p tcp 82 = protokol dan nomor port yang ingin dihapus.
- semanage port -l | grep http = Melihat semua port yang diperbolehkan oleh SELinux untuk tipe http_port_t

- firewall-cmd --permanent --add-port=82/tcp = Menambahkan port 82/tcp ke firewalld secara permanen, agar bisa diakses dari luar (misal lewat browser).
Tanpa ini: Meskipun Apache sudah jalan di port 82, tapi permintaan dari luar akan diblokir oleh firewall.
- firewall-cmd --reload = Memuat ulang konfigurasi firewall agar perubahan yang dilakukan dengan --permanent mulai berlaku.

- groupadd sysadmin = Membuat grup bernama sysadmin, jika belum ada.
- useradd -G sysadmin harry =Membuat user harry, dan menambahkan dia ke grup tambahan sysadmin. Grup utama tetap harry, dan sysadmin jadi grup tambahan.
- echo "password" | passwd --stdin harry = Memberikan password "password" ke user harry secara otomatis (non-interaktif)
- 
