# VM1 Web Sunucusu Kurulumu (Ubuntu 24.04 LTS)

## 1. Sanal Makine Hazırlığı

- VirtualBox üzerinden yeni bir sanal makine oluşturuldu.
- ISO: `ubuntu-24.04-live-server-amd64.iso`
- Kurulum bilgileri:
  - Kullanıcı adı: `mehmet`
  - Şifre: `fener456` (örnek)
  - Hostname: `mehmetvb1`

## 2. Ubuntu Server 24.04 Kurulumu

- “Try or Install Ubuntu Server” ekranından kurulum başlatıldı.
- Minimal kurulum tercih edildi.
- Kurulum sonrası sistem yeniden başlatıldı ve giriş yapıldı.

## 3. Sistem Giriş ve Kontrol

- Giriş başarılı şekilde yapıldı:
  ```bash
  mehmet@mehmetvb1:~$
  
![image](https://github.com/user-attachments/assets/e05a906e-1229-4449-8e7e-01bff0d94c74)

4. Apache ve PHP Kurulumu
Aşağıdaki komutlarla Apache web sunucusu ve PHP yüklendi:
sudo apt update
sudo apt install apache2 php php-mysql libapache2-mod-php unzip wget -y
5. Apache Sunucusunun Doğrulanması
Kurulum sonrası Apache’nin doğru şekilde çalıştığını test etmek için
sanal makinenin IP adresi öğrenildi:
-ip a
![image](https://github.com/user-attachments/assets/2ffe2031-b40b-41d9-8fed-e9ebcd5512c6)

Çıkan sonuçta ip adresi 192.168.1.23 şeklindeydi. Bu ip’ye ana bilgisayardan girince ise
bu sonucu verdi. (Sanal makine ile aynı ortamda çalışması için ağ ayarını Köprü
Bağdaştırıcısı yaptık)

![image](https://github.com/user-attachments/assets/275ba8eb-81dd-4b5d-9608-40708f721991)

Sonuç olarak Apache’nin "It works!" yazan varsayılan karşılama sayfası başarıyla
görüntülendi. Bu, web sunucusunun dışarıdan doğru çalıştığını ve gelen HTTP
isteklerine yanıt verdiğini doğrulamaktadır.

6.SSH Anahtarlı Giriş Yapılandırması (Parola Girişi
Kapatılarak)
Sunucunun güvenliğini artırmak amacıyla, yalnızca SSH anahtarı ile bağlantı kabul edecek şekilde yapılandırma yapılmıştır. Parola ile giriş devre dışı bırakılmıştır.

Ana Makinede SSH Anahtar Üretimi
Ana makinede terminal (PowerShell) açılarak aşağıdaki komut çalıştırıldı:

![image](https://github.com/user-attachments/assets/948685bc-4883-4782-b2d5-dc14084ceb21)

7. Public Key’in Sanal Makineye Eklenmesi
Hem ana bilgisayar hem sanal bilgisayarda iletişim olması için keyler birbiriyle eşleştirildi.
Sanal makinede .ssh dizini oluşturularak yetkiler ayarlandı:
mkdir -p ~/.ssh
chmod 700 ~/.ssh
Ana makinede oluşturulan id_rsa.pub içeriği, VM içerisinde
authorized_keys dosyasına manuel olarak kopyalanarak eklendi:
nano ~/.ssh/authorized_keys
Kopyalanan public key bu dosyaya tek satır olarak eklendi.
chmod 600 ~/.ssh/authorized_keys

![image](https://github.com/user-attachments/assets/351babc6-cc9e-4310-ba2f-04b7b9b6230a)

SSH Servisini Yeniden Başlatma Sırasında Karşılaşılan Sorun ve Çözüm
SSH yapılandırmasında yapılan değişikliklerin etkin olabilmesi için servis yeniden başlatılmak istendiğinde aşağıdaki hata alınmıştır:
sudo systemctl restart ssh
Yüklü olmadığı için kuruldu ve çözüm giderildi.
sudo apt install openssh-server

8.UFW Güvenlik Duvarı Yapılandırması
Sistemde sadece gerekli portların açık olması ve gereksiz tümbağlantıların engellenmesi amacıyla UFW (Uncomplicated Firewall) yapılandırıldı.
1. UFW kurulumu yapıldı:
sudo apt update
sudo apt install ufw –y
2. Gerekli portlara izin verildi:
sudo ufw allow OpenSSH # SSH bağlantısı için (port 22)
sudo ufw allow 80 # HTTP (Apache için)
sudo ufw allow 443 # HTTPS (SSL desteği için)
3. Güvenlik duvarı aktif edilip son durum kontrol edildi:
sudo ufw enable ve sudo ufw status
![image](https://github.com/user-attachments/assets/e9e3187c-1beb-4a64-885a-44c6b5bef8d1)
Bu adımla birlikte yalnızca belirlenen servislerin erişime açık olması sağlandı, diğer tüm portlar kapatıldı. Sistem açıldığında UFW otomatik olarak aktif olacaktır.

9.Apache VirtualHost Yapılandırması ve DomainEşlemesi (bugday.org, buğday.org, 2025ozgur.com)
Bu adımda Apache web sunucusunun birden fazla domaini yönetebilmesi için yapılandırma yapıldı. bugday.org ve buğday.org aynı dizine yönlendirilirken,
2025ozgur.com ve www.2025ozgur.com farklı bir dizine yönlendirildi. İlk olarak gerekli dizinler oluşturuldu ve her dizine örnek bir HTML dosyası konuldu:

sudo mkdir -p /var/www/bugday
sudo mkdir -p /var/www/ozgur
echo "<h1>Buğday Sayfası</h1>" | sudo tee/var/www/bugday/index.html
echo "<h1>2025 Özgür Web Sitesi</h1>" | sudo tee
/var/www/ozgur/index.html
Ardından iki adet sanal host yapılandırma dosyası oluşturuldu:
/etc/apache2/sites-available/bugday.conf
/etc/apache2/sites-available/ozgur.conf
Bu dosyalar etkinleştirildi ve Apache yeniden yüklendi:
sudo a2ensite bugday.conf
sudo a2ensite ozgur.conf
sudo systemctl reload apache2
sudo apache2ctl configtest (hata var mı yok mu diye kontrol ettim)

Windows tarafında domain–IP eşlemesi yapılabilmesi için
C:\Windows\System32\drivers\etc\hosts dosyasına aşağıdaki satırlar eklendi:
192.168.1.37 bugday.org
192.168.1.37 buğday.org
192.168.1.37 2025ozgur.com ve 192.168.1.37 www.2025ozgur.com
Sonuç olarak Apache üzerinde aynı anda birden fazla domain çalışacak şekilde VirtualHost yapısı başarıyla kurulmuş ve test edilmiştir.

10. Veritabanı Sunucusu (VM2) Kurulumu – MariaDB
Bu bölümde, WordPress için ayrı bir sanal makine üzerinde MariaDB veritabanı sunucusu kuruldu ve yapılandırıldı. Giriş çıkış post yayınlama için veritabanı zorunludur.
🖥️1. VM2 Hazırlığı
• VirtualBox üzerinden Ubuntu Server 24.04 ISO dosyası ile yeni bir sanal makine
oluşturuldu.
• Minimum kurulum yapıldı, ek yazılımlar (X sunucusu gibi) yüklenmedi.
• Hostname olarak database belirlendi.
• mehmet adında kullanıcı oluşturuldu.
🔌2. Ağ Ayarları
• VirtualBox → Ayarlar → Ağ:
o "Adaptör Etkinleştirildi"
o Bağlı: Bridged Adapter (veya NAT)
• VM2 açıldıktan sonra ip a komutu ile IP adresi alındığı doğrulandı.
Son durumda IP adresi: 192.168.1.38
🔧3. MariaDB Kurulumu
sudo apt update
sudo apt install mariadb-server -y
• Servis başlatıldı ve otomatik başlatılması sağlandı:
sudo systemctl enable mariadb
sudo systemctl start mariadb
Güvenlik yapılandırmasıyla gereksiz kullanıcılar ve test veritabanı kaldırıldı, root erişimi sınırlandı.
🧰5. WordPress İçin Veritabanı ve Kullanıcı Oluşturma
Wordpress sayfasına giriş ve post oluşturmak için.
mariadbye giriş yapıldı:
sudo mysql
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8mb4 COLLATE
utf8mb4_unicode_ci; CREATE USER 'wpuser'@'%' IDENTIFIED BY 'sifre'; GRANT ALL
PRIVILEGES ON wordpress.* TO 'wpuser'@'%'; FLUSH PRIVILEGES; EXIT;
VM1'den ping 192.168.1.38 komutu ile bağlantı kontrol edildi.
WordPress sunucusu, bu IP üzerinden veritabanı sunucusuna erişebilir hale geldi.
✅Sonuç
• Veritabanı sunucusu kuruldu ve yapılandırıldı.
• WordPress, farklı bir sanal makinede çalışan MariaDB sunucusuna başarıyla
bağlandı.
• Ayrı makine kullanımı sayesinde sistem mimarisi ayrıştırıldı ve daha güvenli hale
getirildi.

11. WordPress Sayfası ve adımları
 ✅1. WordPress Admin Paneline Giriş
• http://192.168.1.37/wp-admin/ üzerinden admin paneline giriş yapıldı.
• Yönetici kullanıcı adı ve şifre ile başarılı giriş sağlandı.
• Admin arayüzü Türkçe olarak ayarlandı.
![image](https://github.com/user-attachments/assets/bc19458e-214a-4504-8d6e-227f713b07fa)
 ✅2. Yeni Yazı Oluşturuldu
• Sol menüden Yazılar → Yeni Ekle menüsü açıldı.
• Başlık olarak: Benim Yeni Yazım yazıldı.
• İçerik kısmına açıklayıcı örnek bir paragraf eklendi.
• Yazı, sağ üstteki Yayınla butonuna tıklanarak yayınlandı.
✅3. Yazıya Dosya Yüklendi
• Yazı düzenleme ekranında + butonu ile "Dosya" bloğu eklendi.
• Bilgisayardan örnek bir .png veya .pdf dosyası yüklendi.
• Dosya, yazı içinde görünür hale getirildi ve yazı tekrar güncellendi.
✅4. SEO Uyumlu Kalıcı Bağlantı Ayarı Yapıldı
• Sol menüden: Ayarlar → Kalıcı Bağlantılar menüsüne girildi.
• "Yazı ismi" seçeneği işaretlendi.
• Sayfanın alt kısmından "Değişiklikleri Kaydet" butonuna tıklandı.
 Artık yayınlanan yazı aşağıdaki gibi SEO dostu bir URL ile erişilebilir durumdadır

![image](https://github.com/user-attachments/assets/58243e47-b944-4c5a-b457-04407e84dff7)

12.2025ozgur.com Ana Sayfası – 100 satırlık bildirim ekranı
Bu adımda, 2025ozgur.com adresinde 100 satır boyunca "Kullanıcılarımın kişisel verilerini toplamayacağım." cümlesini içeren basit bir HTML sayfası oluşturulmuştur.
✅1. Web Dizin Yapısı Oluşturuldu
sudo mkdir -p /var/www/ozgur
for i in {1..100}; do echo "Kullanıcılarımın kişisel verilerini toplamayacağım." | sudo tee -a /var/www/ozgur/index.html > /dev/null; done 
100 defa bildiri içeriğini yazdırdım döngüyle 
![image](https://github.com/user-attachments/assets/4e069532-d5c7-457a-b4f8-e9b8a7bd7da9)

13. Apache Yapılandırması
ozgur.conf dosyasında 2025ozgur.com ve www.2025ozgur.com domainlerininApache tarafından tanınması sağlandı. Bu yapılandırma, gelen HTTP isteklerinin doğru dizine (DocumentRoot) yönlendirilmesi ve her domainin kendi içeriğini sunabilmesi için zorunludur.
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
Bu yönlendirme ile hem 2025ozgur.com hem www.2025ozgur.com a aynı anda erişim oldu.

14. Parola Korumalı Yönetim Sayfası –2025ozgur.com/yonetim
Bu adımda, 2025ozgur.com/yonetim dizini parola korumasına alınmış ve içerisine basit bir HTML dosyası yerleştirilmiştir.
✅1. Dizin Oluşturuldu
Apache dizin yapısına uygun olarak yeni bir dizin oluşturuldu:
sudo mkdir -p /var/www/ozgur/yonetim
✅2. Parola Dosyası Oluşturuldu
sudo apt install apache2-utils -y
sudo htpasswd -c /etc/apache2/.htpasswd ad.soyad
✅3. Apache Yapılandırmasına Basic Giriş Eklendi
<Directory "/var/www/ozgur/yonetim">
 AuthType Basic
 AuthName "Yetkili Giriş"
 AuthUserFile /etc/apache2/.htpasswd
 Require valid-user
</Directory>
✅4. HTML Dosyası Oluşturuldu
/var/www/ozgur/yonetim/index.html dosyası oluşturuldu ve içerik eklendi
![image](https://github.com/user-attachments/assets/6a941163-3a52-489c-9308-254669aef30e)

Mehmet Kordon mehmetkordon09@gmail.com



