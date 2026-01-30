# Установка нужных пакетов
```bash
apt install php php-common libapache2 mod-php php-bz2 php-gd php-mysql php-curl php-mbstring php-imagick php-zip php-common php-curl php-xml php-json php-bcmath php-xml php-intl php-gmp zip unzip wget smbclient ffmpeg php-sqlite3 php-smbclient php-imap apache2 mariadb-server
```
# Настройка MySQL
##### Вход 
```bash
mysql
```
##### Создание БД
```bash
CREATE USER 'ncloud'@'localhost' IDENTIFIED BY 'admin123';
CREATE DATABASE ncloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
GRANT ALL PRIVILEGES ON ncloud.* TO 'ncloud'@'localhost';
FLUSH PRIVILEGES;
quit;
```
##### Перезапуск БД
```bash
systemctl enable--now mariadb

systemctl status mariadb
```
# Установка Nextcloud 
```bash
cd /var/www

wget https://download.nextcloud.com/server/releases/latest.tar.bz2

tar -xvf latest.tar.bz2

mkdir -p /var/www/nextcloud/data

chown -R www-data:www-data /var/www/nextcloud/
```
# Настройка Apache 
```bash
nano /etc/apache2/sites-available/nextcloud.conf

Alias /nextcloud "/var/www/nextcloud/"

<Directory /var/www/nextcloud/>
  Require all granted
  AllowOverride All
  Options FollowSymLinks MultiViews

  <IfModule mod_dav.c>
    Dav off
  </IfModule>

</Directory>

a2ensite nextcloud.conf

systemctl reload apache2
```
# Настройка SSL
```bash
mkdir -p /etc/apache2/ssl

openssl req -x509 -nodes -days 365 -newkey rsa:4096 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt

a2enmod ssl

nano /etc/apache2/sites-available/default-ssl.conf

#поменять
SSLCertificateFile /etc/ssl/certs/ssl-cert-snakeoil.pem
SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key

#На

SSLCertificateFile /etc/apache2/ssl/apache.crt
SSLCertificateKeyFile /etc/apache2/ssl/apache.key

a2ensite default-ssl.conf

nano /etc/apache2/sites-available/000-default.conf

#Все удаляем и пишем

<VirtualHost *:80>
   ServerAdmin example@example

   RewriteEngine On
   RewriteCond %{HTTPS} off
   RewriteRule ^(.*)$ https://%{HTTP_HOST}$1 [R=301,L]
</VirtualHost>

a2enmod rewrite
service apache2 restart
```
# Логин 
```bash
ip
```
# Повышение лимитов загрузки 
##### Текущие значения 
```bash
grep -E "upload_max_filesize|post_max_size|memory_limit|max_execution_time|max_input_vars|max_input_time" /etc/php/8.3/fpm/php.ini
```
##### Повышения 
```bash
sed -i 's/^upload_max_filesize.*/upload_max_filesize = 16G/; s/^post_max_size.*/post_max_size = 16G/; s/^memory_limit.*/memory_limit = 2048M/; s/^max_execution_time.*/max_execution_time = 3600/; s/^;max_input_vars.*/max_input_vars = 3600/; s/^max_input_time.*/max_input_time = 3600/' /etc/php/8.3/fpm/php.ini
```
##### Проверка
```bash
grep -E "upload_max_filesize|post_max_size|memory_limit|max_execution_time|max_input_vars|max_input_time" /etc/php/8.3/fpm/php.ini
```
# Активация OPCache в PHP
```bash
nano /etc/php/8.4/apache2/conf.d/10-opcache.ini

zend_extension=opcache.so
opcache.enable=1
opcache.enable_cli=1
opcache.interned_strings_buffer=64
opcache.max_accelerated_files=12000
opcache.memory_consumption=512
opcache.save_comments=1
opcache.revalidate_freq=60
opcache.jit=on
opcache.jit = 1255
opcache.jit_buffer_size = 256M
```
##### Перезапуск Apache2 
```
systemctl restart apache2
```
# Активация APCu в PHP
###### Установка 
```bash
apt install php-apcu
```
###### Настройка
```bash
nano /etc/php/8.4/apache2/conf.d/20-apcu.ini

extension=apcu.so
apc.enable_cli=1
```
##### Настройка NC
```bash
nano /var/www/nextcloud/config/config.php

'memcache.local' => '\OC\Memcache\APCu',
```
##### Перезапуск Apache2 
```
systemctl restart apache2
```
# Важные для работы команды 
```bash
sudo -u www-data php /var/www/nextcloud/occ maintenance:repair --include-expensive

sudo -u www-data php /var/www/nextcloud/occ db:add-missing-indices

sudo -u www-data php /var/www/nextcloud/occ config:system:set maintenance_window_start --type=integer --value=1
```
# Настройка Cron для очистки 
```bash
sudo crontab -u www-data -e

*/5  *  *  *  * php -f /var/www/nextcloud/cron.php
```

```bash
sudo -u www-data php /var/www/nextcloud/occ files:scan --all

sudo -u www-data php /var/www/nextcloud/occ app:update --all

```
