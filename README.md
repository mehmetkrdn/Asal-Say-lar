VM1 Web Sunucusu Kurulumu (Ubuntu 24.04 LTS)

1. Sanal Makine Hazırlığı

VirtualBox ile yeni bir sanal makine oluşturuldu.

ISO: ubuntu-24.04-live-server-amd64.iso

Kurulum bilgileri:

Kullanıcı adı: mehmet

Şifre: fener456

Hostname: mehmetvb1

2. Ubuntu Server Kurulumu

"Try or Install Ubuntu Server" ile başlatıldı.

Minimal kurulum yapıldı.

Sistem yeniden başlatıldı.

3. Sistem Giriş ve Kontrol

mehmet@mehmetvb1:~$

4. Apache ve PHP Kurulumu

sudo apt update
sudo apt install apache2 php php-mysql libapache2-mod-php unzip wget -y

