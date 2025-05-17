VM1 Web Sunucusu Kurulumu (Ubuntu 24.04 LTS)

## 1. Sanal Makine Hazırlığı

VirtualBox üzerinden yeni bir sanal makine oluşturuldu.

ISO olarak ubuntu-24.04-live-server-amd64.iso kullanıldı.

Kurulum sırasında aşağıdaki bilgiler girildi:
```bash
Kullanıcı adı: mehmet
Şifre: fener456 (örnek)
Hostname: mehmetvb1
```
![image](https://github.com/user-attachments/assets/f17832a4-53fc-4c1f-9cb5-5dfb1e5687f1)


## 2. Ubuntu Server 24.04 Kurulumu

“Try or Install Ubuntu Server” ekranından kurulum başlatıldı.

Minimal kurulum tercih edildi.

Kurulum tamamlandıktan sonra sistem yeniden başlatıldı ve giriş yapıldı.

## 3. Sistem Giriş ve Kontrol

Giriş başarılı bir şekilde gerçekleştirildi:
mehmet@mehmetvb1:~$

## 4. Apache ve PHP Kurulumu

Web sayfalarını sunmak ve dinamik PHP içerikleri çalıştırmak için Apache ve PHP kurulumu yapıldı:
```bash
sudo apt update
sudo apt install apache2 php php-mysql libapache2-mod-php unzip wget -y
```
## 5. Apache Sunucusunun Doğrulanması
Kurulum sonrası Apache’nin doğru şekilde çalıştığını test etmek için sanal makinenin IP adresi öğrenildi:
```bash
-ip a
```
![image](https://github.com/user-attachments/assets/1c01259d-6b49-4ccd-945c-0d0b2c106ea8)

Çıkan sonuçta ip adresi 192.168.1.23 şeklindeydi. Bu ip’ye ana bilgisayardan girince ise bu sonucu verdi. (Sanal makine ile aynı ortamda çalışması için ağ ayarını Köprü Bağdaştırıcısı yaptık)

![image](https://github.com/user-attachments/assets/c4ace834-f165-4aee-b6fa-abf5ea6ff724)

Sonuç olarak Apache’nin "It works!" yazan varsayılan karşılama sayfası başarıylagörüntülendi. Bu, web sunucusunun dışarıdan doğru çalıştığını ve gelen HTTP
isteklerine yanıt verdiğini doğrulamaktadır.


## 6.SSH Anahtarlı Giriş Yapılandırması (Parola Girişi Kapatılarak)
Sunucunun güvenliğini artırmak amacıyla, yalnızca SSH anahtarı ile bağlantı kabuledecek şekilde yapılandırma yapılmıştır. Parola ile giriş devre dışı bırakılmıştır.
Ana Makinede SSH Anahtar Üretimi
Ana makinede terminal (PowerShell) açılarak aşağıdaki komut çalıştırıldı:
![image](https://github.com/user-attachments/assets/c6bbbd73-e01f-4cf3-a045-f826cddb98bf)

## 7. 2. Public Key’in Sanal Makineye Eklenmesi
Sanal makinede .ssh dizini oluşturularak yetkiler ayarlandı:
```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
Ana makinede oluşturulan id_rsa.pub içeriği, VM içerisinde
authorized_keys dosyasına manuel olarak kopyalanarak eklendi:
nano ~/.ssh/authorized_keys
Kopyalanan public key bu dosyaya tek satır olarak eklendi.
chmod 600 ~/.ssh/authorized_keys
```
![image](https://github.com/user-attachments/assets/c9afdd42-874c-4346-b373-6cb1ac4be2d7)

## SSH Servisini Yeniden Başlatma Sırasında KarşılaşılanSorun ve Çözüm
SSH yapılandırmasında yapılan değişikliklerin etkin olabilmesi için servis yeniden başlatılmak istendiğinde aşağıdaki hata alınmıştır:
```bash
sudo systemctl restart ssh
Yüklü olmadığı için kuruldu ve çözüm giderildi.
sudo apt install openssh-server
```
## 8. UFW Güvenlik Duvarı Yapılandırması
Sistemde sadece gerekli portların açık olması ve gereksiz tümbağlantıların engellenmesi amacıyla UFW (Uncomplicated Firewall)yapılandırıldı.
## 8.1. UFW kurulumu yapıldı:
```bash
sudo apt update
sudo apt install ufw –y
```
## 8.2. Gerekli portlara izin verildi:
```bash
sudo ufw allow OpenSSH # SSH bağlantısı için (port 22)
sudo ufw allow 80 # HTTP (Apache için)
sudo ufw allow 443 # HTTPS (SSL desteği için)
```
## 8.3. Güvenlik duvarı aktif edilip son durum kontrol edildi:
```bash
sudo ufw enable ve sudo ufw status
```
![image](https://github.com/user-attachments/assets/00a201fb-d79c-483b-ac1e-5c1cd19c09fb)

Bu adımla birlikte yalnızca belirlenen servislerin erişime açık olması sağlandı, diğer tüm portlar kapatıldı. Sistem açıldığında UFW otomatik olarak aktif olacaktır.

## 9. Apache VirtualHost Yapılandırması ve Domain Eşlemesi (bugday.org, buğday.org, 2025ozgur.com)
Bu adımda Apache web sunucusunun birden fazla domaini yönetebilmesi içinyapılandırma yapıldı. bugday.org ve buğday.org aynı dizine yönlendirilirken, 2025ozgur.com ve www.2025ozgur.com farklı bir dizine yönlendirildi. İlk olarak gerekli dizinler oluşturuldu ve her dizine örnek bir HTML dosyası konuldu:
```bash
sudo mkdir -p /var/www/bugday
sudo mkdir -p /var/www/ozgur
echo "<h1>Buğday Sayfası</h1>" | sudo tee/var/www/bugday/index.html
echo "<h1>2025 Özgür Web Sitesi</h1>" | sudo tee /var/www/ozgur/index.html
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
192.168.1.37 2025ozgur.com
192.168.1.37 www.2025ozgur.com
```
Sonuç olarak Apache üzerinde aynı anda birden fazla domain çalışacak şekilde VirtualHost yapısı başarıyla kurulmuş ve test edilmiştir.

## 10. 🗄️Veritabanı Sunucusu (VM2) Kurulumu – MariaDB
Bu bölümde, WordPressde giriş post yükleme gibi şeyler. için ayrı bir sanal makine üzerinde MariaDB veritabanı sunucusu kuruldu ve yapılandırıldı

![image](https://github.com/user-attachments/assets/21bed3fc-c827-41d1-b547-b46ece55f0d9)

## 10.1 VM2 Hazırlığı
• VirtualBox üzerinden Ubuntu Server 24.04 ISO dosyası ile yeni bir sanal makine oluşturuldu.
• Minimum kurulum yapıldı, ek yazılımlar (X sunucusu gibi) yüklenmedi.
• Hostname olarak database belirlendi.
• mehmet adında kullanıcı oluşturuldu.

## 10.2 Ağ Ayarları
• VirtualBox → Ayarlar → Ağ:
o "Adaptör Etkinleştirildi"
o Bağlı: Bridged Adapter (veya NAT)
• VM2 açıldıktan sonra ip a komutu ile IP adresi alındığı doğrulandı.
Son durumda IP adresi: 192.168.1.38

## 10.3 MariaDB Kurulumu
```bash
sudo apt update
sudo apt install mariadb-server -y
• Servis başlatıldı ve otomatik başlatılması sağlandı:
sudo systemctl enable mariadb
sudo systemctl start mariadb
```
Güvenlik yapılandırmasıyla gereksiz kullanıcılar ve test veritabanı kaldırıldı, root erişimi
sınırlandı.

## 10.4 WordPress İçin Veritabanı ve Kullanıcı Oluşturma
Wordpress sayfasına giriş ve post oluşturmak için. mariadbye giriş yapıldı:
```bash
sudo mysql
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8mb4 COLLATE
utf8mb4_unicode_ci; CREATE USER 'wpuser'@'%' IDENTIFIED BY 'sifre'; GRANT ALL
PRIVILEGES ON wordpress.* TO 'wpuser'@'%'; FLUSH PRIVILEGES; EXIT;
VM1'den ping 192.168.1.38 komutu ile bağlantı kontrol edildi.
```
WordPress sunucusu, bu IP üzerinden veritabanı sunucusuna erişebilir hale geldi.
## ✅Sonuç
• Veritabanı sunucusu kuruldu ve yapılandırıldı.
• WordPress, farklı bir sanal makinede çalışan MariaDB sunucusuna başarıyla
bağlandı.
• Ayrı makine kullanımı sayesinde sistem mimarisi ayrıştırıldı ve daha güvenli hale
getirildi.

## 11.1 WordPress Admin Paneline Giriş
• http://bugday.org/wp-admin/ veya http://192.168.1.37/wp-admin/üzerinden admin paneline giriş yapıldı.
• Yönetici kullanıcı adı ve şifre ile başarılı giriş sağlandı.
• Admin arayüzü Türkçe olarak görüntülendi.
![image](https://github.com/user-attachments/assets/fecedb51-ee14-42f0-b36c-da22b648873f)

## 11.2 Yeni Yazı Oluşturuldu
• Sol menüden Yazılar → Yeni Ekle menüsü açıldı.
• Başlık olarak: Benim Yeni Yazım yazıldı.
• İçerik kısmına açıklayıcı örnek bir paragraf eklendi.
• Yazı, sağ üstteki Yayınla butonuna tıklanarak yayınlandı.
## 11.3. Yazıya Dosya Yüklendi
• Yazı düzenleme ekranında + butonu ile "Dosya" bloğu eklendi.
• Bilgisayardan örnek bir .png veya .pdf dosyası yüklendi.
• Dosya, yazı içinde görünür hale getirildi ve yazı tekrar güncellendi.

## 11.4 SEO Uyumlu Kalıcı Bağlantı Ayarı Yapıldı
• Sol menüden: Ayarlar → Kalıcı Bağlantılar menüsüne girildi.
• "Yazı ismi" seçeneği işaretlendi.
• Sayfanın alt kısmından "Değişiklikleri Kaydet" butonuna tıklandı.
 Artık yayınlanan yazı aşağıdaki gibi SEO dostu bir URL ile erişilebilir durumdadır
 
 ![image](https://github.com/user-attachments/assets/2bfb7160-5422-4546-bd6c-fcdbfea170ad)
 
 Wordpress sayfamamız da yeni bir post yayınlayıp bir dosya yükledik.

## 12. 2025ozgur.com Ana Sayfası – 100 satırlık bildirim 
Bu adımda, 2025ozgur.com adresinde 100 satır boyunca "Kullanıcılarımın kişisel verilerini toplamayacağım." cümlesini içeren basit bir HTML sayfası oluşturulmuştur.
## 12.1 Web Dizin Yapısı Oluşturuldu
100 defa bildiri içeriğini yazdırdım döngüyle
```bash
sudo mkdir -p /var/www/ozgur
for i in {1..100}; do echo "Kullanıcılarımın kişisel verilerini toplamayacağım." | sudo tee -
a /var/www/ozgur/index.html > /dev/null; done
```
![image](https://github.com/user-attachments/assets/33dffd19-329b-4962-8077-4492a158228a)

## 12.2 Apache Yapılandırması
ozgur.conf dosyasında 2025ozgur.com ve www.2025ozgur.com domainlerinin Apache tarafından tanınması sağlandı. Bu yapılandırma, gelen HTTP isteklerinin doğru
dizine (DocumentRoot) yönlendirilmesi ve her domainin kendi içeriğini sunabilmesi için zorunludur.
```bash
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
```
Bu yönlendirme ile hem 2025ozgur.com hem www.2025ozgur.com a aynı anda erişim
oldu.

# 13 Parola Korumalı Yönetim Sayfası –2025ozgur.com/yonetim
Bu adımda, 2025ozgur.com/yonetim dizini parola korumasına alınmış ve içerisine basit bir HTML dosyası yerleştirilmiştir.
## 13.1 Dizin Oluşturuldu
Apache dizin yapısına uygun olarak yeni bir dizin oluşturuldu:
```bash
sudo mkdir -p /var/www/ozgur/yonetim
```
## 13.2 Parola Dosyası Oluşturuldu
```bash
sudo apt install apache2-utils -y
sudo htpasswd -c /etc/apache2/.htpasswd ad.soyad
```
## 13.3 Apache Yapılandırmasına Basic Giriş Eklendi
```bash
<Directory "/var/www/ozgur/yonetim">
 AuthType Basic
 AuthName "Yetkili Giriş"
 AuthUserFile /etc/apache2/.htpasswd
 Require valid-user
</Directory>
```
## 13.4 HTML Dosyası Oluşturuldu
/var/www/ozgur/yonetim/index.html dosyası oluşturuldu ve içerik eklendi

![image](https://github.com/user-attachments/assets/b7158533-e7e1-44e3-ba76-016280b0166c)


Mehmet Kordon 
mehmetkordon09@gmail.com

