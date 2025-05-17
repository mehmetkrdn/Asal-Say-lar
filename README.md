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

