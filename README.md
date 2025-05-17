VM1 Web Sunucusu Kurulumu (Ubuntu 24.04 LTS)

1. Sanal Makine Hazırlığı

VirtualBox üzerinden yeni bir sanal makine oluşturuldu.

ISO: ubuntu-24.04-live-server-amd64.iso

Kurulum bilgileri:

Kullanıcı adı: mehmet

Şifre: fener456 ("örnek")

Hostname: mehmetvb1

2. Ubuntu Server 24.04 Kurulumu

"Try or Install Ubuntu Server" ekranından kurulum başlatıldı.

Minimal kurulum tercih edildi.

Sistem yeniden başlatıldı ve giriş yapıldı.

3. Sistem Giriş ve Kontrol

mehmet@mehmetvb1:~$



4. Apache ve PHP Kurulumu

sudo apt update
sudo apt install apache2 php php-mysql libapache2-mod-php unzip wget -y

5. Apache Sunucusunun Doğrulanması

IP adresi öğrenildi: ip a


Örnek IP: 192.168.1.23

Ağ ayarı: Köprü Bağdaştırıcı



6. SSH Anahtarlı Giriş (Parola Girişi Kapalı)

Ana makinede SSH anahtarı üretildi:


Sanal makinada .ssh dizini oluşturuldu:

mkdir -p ~/.ssh
chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys



SSH servisi yeniden başlatıldı, sorun alınıp aşağıdaki komutla çözüldü:

sudo apt install openssh-server

7. UFW Güvenlik Duvarı Yapılandırması

sudo apt update
sudo apt install ufw -y
sudo ufw allow OpenSSH
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable
sudo ufw status



8. Apache VirtualHost & Domain Ayarları

sudo mkdir -p /var/www/bugday
sudo mkdir -p /var/www/ozgur
sudo tee /var/www/bugday/index.html <<< "<h1>Buğday Sayfası</h1>"
sudo tee /var/www/ozgur/index.html <<< "<h1>2025 Özgür Web Sitesi</h1>"

VirtualHost dosyaları:

/etc/apache2/sites-available/bugday.conf

/etc/apache2/sites-available/ozgur.conf

sudo a2ensite bugday.conf
sudo a2ensite ozgur.conf
sudo systemctl reload apache2
sudo apache2ctl configtest

Windows hosts dosyasına:

192.168.1.37 bugday.org
192.168.1.37 buğday.org
192.168.1.37 2025ozgur.com
192.168.1.37 www.2025ozgur.com

9. VM2 Veritabanı Sunucusu - MariaDB

Hostname: database, Kullanıcı: mehmet

IP: 192.168.1.38

sudo apt update
sudo apt install mariadb-server -y
sudo systemctl enable mariadb
sudo systemctl start mariadb

WordPress Veritabanı ve Kullanıcı

sudo mysql
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'wpuser'@'%' IDENTIFIED BY 'sifre';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'%';
FLUSH PRIVILEGES;
EXIT;

10. WordPress Kurulum ve Test

Admin Panel Girişi

http://192.168.1.37/wp-admin/


Yazı Oluşturma & Dosya Ekleme

Yazı: "Benim Yeni Yazım"

Dosya yüklendi (png/pdf)

Yayınlandı

SEO Kalıcı Bağlantı



11. 2025ozgur.com - KVK Bildirimi Sayfası

sudo mkdir -p /var/www/ozgur
for i in {1..100}; do echo "Kullanıcılarımın kişisel verilerini toplamayacağım." | sudo tee -a /var/www/ozgur/index.html > /dev/null; done



12. ozgur.conf Apache Ayarı

<VirtualHost *:80>
    ServerName 2025ozgur.com
    ServerAlias www.2025ozgur.com
    DocumentRoot /var/www/ozgur
    <Directory /var/www/ozgur>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>

13. Parola Korumalı Yönetim Sayfası (/yonetim)

sudo mkdir -p /var/www/ozgur/yonetim
sudo apt install apache2-utils -y
sudo htpasswd -c /etc/apache2/.htpasswd ad.soyad

Apache Basic Auth Ayarı:

<Directory "/var/www/ozgur/yonetim">
    AuthType Basic
    AuthName "Yetkili Giriş"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</Directory>

/var/www/ozgur/yonetim/index.html dosyası oluşturuldu.


Hazırlayan: Mehmet KordonE-posta: mehmetkordon09@gmail.com

