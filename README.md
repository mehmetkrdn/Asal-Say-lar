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

# 9. Apache VirtualHost Yapılandırması ve Domain Eşlemesi (bugday.org, buğday.org, 2025ozgur.com)
Bu adımda Apache web sunucusunun birden fazla domaini yönetebilmesi içinyapılandırma yapıldı. bugday.org ve buğday.org aynı dizine yönlendirilirken, 2025ozgur.com ve www.2025ozgur.com farklı bir dizine yönlendirildi. İlk olarak gerekli dizinler oluşturuldu ve her dizine örnek bir HTML dosyası konuldu:
```bash
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
192.168.1.37 2025ozgur.com
192.168.1.37 www.2025ozgur.com
```
Sonuç olarak Apache üzerinde aynı anda birden fazla domain çalışacak şekilde VirtualHost yapısı başarıyla kurulmuş ve test edilmiştir.



