# Catatan-belajar-REDHAT
- nmtui = perintah untuk menyeting IP Address

#NO.1
- Ketika vm redhat dijalankan, pada tampilan awal restart klik "e"
- Tambahkan "rw init=/bin/bash" di baris akhir initrd
- lalu tekan Ctrl+x
- lalu masukan perintah "passwd" untuk merubah password root, lalu masukan password rootnya,lalu enter
- touch /.autorelabel = membuat file
- /sbin/reboot -f = merestar sistem
perintah di atas berutujuan untuk merubah password root yang tak bisa login dimenu login

#NO.2

Cara menyeting IPV4Address
- nmcli connection modify enp0s3 autoconnect yes ipv4.method manual ipv4.addresses 172....... ipv4.gateway 172........ ipv4.dns 202.......

Cara menyeting IPV4Address ke setingan default DHCP
- nmcli connection modify enp0s3 ipv4.method auto
- nmcli connection modify enp0s3 ipv4.addresses "" ipv4.gateway "" ipv4.dns ""
- nmcli connection down enp0s3
- nmcli connection up enp0s3
  
- chage -m 7 root	= Set minimum 7 hari sebelum bisa ganti password lagi.
- chage -W 5 root	= Tampilkan peringatan 5 hari sebelum password expired.
- chage -E 2025-12-31 root = Set akun root expired pada 31 Desember 2025.
- chage -l root = Menampilkan informasi masa berlaku password user root.

#NO.3
- yum install httpd -y = perintah untuk menginstal httpd jikan belum terinstal
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

#NO.4
- groupadd sysadm = Membuat grup bernama sysadm, jika belum ada.
- useradd -G sysadm harry = Membuat user harry, dan menambahkan dia ke grup tambahan sysadm. Grup utama tetap harry, dan sysadm jadi grup tambahan.
- useradd -s /sbin/nologin sarah = Perintah ini membuat user bernama sarah, tapi user tersebut tidak bisa login ke shell.
  KET : Opsi -s /sbin/nologin biasanya dipakai bukan untuk user manusia yang akan login, tapi untuk user sistem atau user khusus, biasanya digunakan untuk kepemilikan file/direktory contoh user nologin yang umum dibuat = User 'www-data' (untuk Apache di Debian/Ubuntu), User 'nginx' (untuk Nginx), dan User 'mysql' (untuk MySQL)
- echo "user1234" | passwd --stdin harry = Memberikan password "user1234" ke user harry secara otomatis (non-interaktif)

- which useradd = untuk mengetahui lokasi lengkap binary perintah useradd, hasilnya "/usr/sbin/useradd" Ini path lengkap yang nanti kamu pakai di konfigurasi sudoers
- visudo = perintah untuk mengedit file sudoers
  lalu cari bagian "%wheel ALL=(ALL) ALL", lalu tambahkan perintah dibagian bawahnya
  1. ALL=/usr/sbin/useradd (Penjelasan: %sysadm = semua user yang menjadi member group sysadmin. Diizinkan menjalankan perintah /usr/sbin/useradd.Hanya perintah ini saja, tidak yang lain.)
  2. harry  ALL=(ALL)       NOPASSWD: /usr/bin/passwd (User harry Bisa menjalankan /usr/bin/passwd tanpa diminta password sudo.)
 
#NO.5
- sudo vi /etc/yum.repos.d/local.repo = membuat file bernama local.repo dan langsung mengeditnya, lalu ketikan perintah berikut di dalam file local.repo
  [Repo-BaseOS]
  name=BaseOS
  baseurl=http://foundation0.ilt.example.com/dvd/BaseOS
  enabled=1
  gpgcheck=0

  [Repo-AppStream]
  name=AppStream
  baseurl=http://foundation0.ilt.example.com/dvd/AppStream
  enabled=1
  gpgcheck=0
- ls /etc/yum.repos.d/ = untuk melihat file local.repo sudah dibuat atau belum
- sudo yum clean all = Membersihkan cache lama
- sudo yum repolist = Menampilkan daftar repository yang aktif

- sudo vi /etc/hosts = mengedit file hosts dengan mengetikan di dalamnya "192.168.18.200   foundation0.ilt.example.com"
- sudo vi /etc/chrony.conf = Konfigurasi NTP server supaya sinkron waktu dengan mengetikan di dalamnya perintah  "server foundation0.ilt.example.com iburst"
- sudo systemctl restart chronyd.service =  merestart chrony
- sudo systemctl enable/disable chronyd
- sudo systemctl status chronyd = mengecek status chrony
- chronyc sources -v = mengecek status ntp

#NO.6
- mkdir -p /shared/sysadm = membuat direktori bernama sysadm
- chown root:sysadm /shared/sysadm = setel kepemilikan direktory dengan Ownernya tetap root dan Group ownership menjadi sysadm
- chmod 2770 /shared/sysadm = Atur permission supaya hanya grup sysadm yang bisa mengakses, membaca, menulis
  1. Mengapa 2770?
    2 = Setgid bit (penting agar file yang dibuat otomatis mewarisi group ownership sysadm).
    7 (rwx) = owner (root) bisa membaca/menulis/mengeksekusi.
    7 (rwx) = group sysadm bisa membaca/menulis/mengeksekusi.
    0 = user lain tidak punya akses sama sekali.
- ls -ld /shared/sysadm = Cek ownership dan permission









