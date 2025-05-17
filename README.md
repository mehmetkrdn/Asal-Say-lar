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



