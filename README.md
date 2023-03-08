
Step To Prepare Server For Laravel
----

1. Setup DNS
2. Setup Subdomain if any
3. Install `update, vim, zip, git, htop, nvm, composer, bower`
4. Install Webmin, Apache2, PHP, MySQL
5. 

Prepare User Login For Server
---

Add new user: `sudo adduser [username]`

Add user to sudoer: `sudo usermod -aG sudo [username]`

Passwordless SSH

1. Copy your RSA public key
2. Create text file in `rsa/authorized_keys`
3. Paste on it

```
mkdir ~/.ssh
vim ~/.ssh/authorized_keys
```

Management User(Linux)
---

**Membuat `user` baru dan menambahkan ke `group`**

`useradd -g [group-name] [user-name]`

Example:

- `useradd -g www-data john`
- `useradd -g www-data jane`

**Menambahkan `group` ke `user` yang sudah pernah dibuat**

`usermod -a -G [group-name] [user-name]`

Example:

- `usermod -a -G www-data john`
- `usermod -a -G www-data jane`

**Check User didalam Group**

Untuk mengecheck user apa saja yang ada didalam sebuah group

`grep ^[group-name] /etc/group`

SSH
---

Get public key for `authorized_keys`: `ssh-keygen -y -f /path_to_downloaded_key-pair.pem`


Install All Dependencies
----

```
sudo apt update
sudo apt install zip git
```

Check Ubuntu Version: `lsb_release -a`
Check Public IP: `curl ifconfig.co`

Install Webmin
---

1. Download `.deb`. Type `wget http://prdownloads.sourceforge.net/webadmin/webmin_1.990_all.deb`. References: https://www.webmin.com/deb.html
2. Install it. `sudo apt install [path-file]`. Ex: `sudo apt install ~/webmin_1.990_all.deb`

NOTE: Check your server port setting if you couldn't access your server via IP. It might cause:

1. Firewall
2. Server configuration. For example if you use AWS, you must add manually on account setting(via WEB)

Install PHP
---

Untuk ini, akan menggunakan versi default tiap OS linux. Contoh Ubuntu 18.04, akan menginstall 7.2

`sudo apt install php php-cli php-fpm php-json php-common php-mysql php-zip php-gd php-mbstring php-curl php-xml php-pear php-bcmath libapache2-mod-php`

**PHP 7.4 untuk Ubuntu 18.04**

```
sudo apt install software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt update
```

`sudo apt install php7.4 php7.4-cli php7.4-fpm php7.4-json php7.4-common php7.4-mysql php7.4-zip php7.4-gd php7.4-mbstring php7.4-curl php7.4-xml php7.4-bcmath libapache2-mod-php7.4`

**PHP 8.1 untuk Ubuntu 18.04**



**Cara Uninstall PHP**

```
sudo apt-get purge php7.*
sudo apt-get autoclean
sudo apt-get autoremove
```

Ref: [Uninstall php 7 in ubuntu 18.04](https://stackoverflow.com/questions/58045685/uninstall-php-7-in-ubuntu-18-04)

Switch Between Different PHP Version
----

`sudo update-alternatives --config php`

Install Apache
----

`sudo apt install apache2`

Install MySQL
----

`sudo apt install mysql-server`

Secure MySQL

`sudo mysql_secure_installation`

Create new user for MySQL

Changes `sammy` to your username
Change `password` to your username password

```
sudo mysql -u root
CREATE USER 'sammy'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON *.* TO 'sammy'@'localhost' WITH GRANT OPTION;
```

Install MariaDB
----

```
sudo apt update
sudo apt install mariadb-server
sudo mysql_secure_installation
```

Ref: [How to Install MariaDB on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-mariadb-on-ubuntu-18-04)

Install PhpMyAdmin
----

1. Download file `wget https://files.phpmyadmin.net/phpMyAdmin/5.1.3/phpMyAdmin-5.1.3-all-languages.zip`. References: https://www.phpmyadmin.net/
2. Extract to `/var/www/` using `sudo unzip [filename] -d /var/www`. No need to mention folder, because zip file is contain folder.
3. Rename folder `cd /var/www && sudo mv phpMyAdmin-5.1.3-all-languages/ phpmyadmin`
4. Fix permission `sudo chown [username]:www-data -R /var/www/phpmyadmin`

Install Composer
----

1. Go To https://getcomposer.org/download/
2. Run something like this

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === '906a84df04cea2aa72f40b5f787e49f22d4c2f19492ac310e8cba5b96ac8b64115ac402c8cd292b8a03482574915d1a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

3. Configure to run globally `sudo mv composer.phar /usr/local/bin/composer`

Install NVM
-----

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```

Check available version: `nvm ls-remote`
Install NPM: `npm install [version]`
Set Default: `npm alias default [version]`


Install Bower
----

`npm install -g bower`

Fix Permission
----

Fix Owner: `sudo chown [username]:www-data -R /var/www/phpmyadmin`
Fix Permission:

Fix Laravel Permission
----

```
sudo chown -R [username]:www-data .
sudo find . -type f -exec chmod 664 {} \;
sudo find . -type d -exec chmod 775 {} \;
sudo chgrp -R www-data storage bootstrap/cache
sudo chmod -R ug+rwx storage bootstrap/cache
```

Install Laravel
----

```
cp .env.example .env
composer install
php artisan key:generate && php artisan jwt:secret
```

Cert Bot Let's Encrypt
---
References: https://certbot.eff.org/instructions?ws=apache&os=ubuntufocal

```
sudo apt install snapd
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --apache
```

GIT
---

Git Ignore Permission: `git config core.fileMode false`


Laravel Troubleshooting
-------

When accessing route something like `domain.com/forgot-password` got response **404 Not Found**. It might because:

1. Mod Rewrite disabled
2. Apache `.conf` didn't setup correctly

Enable Mod Rewrite: `sudo a2enmod rewrite`

Setup apache `.conf`: Go to your virtual host domain `.conf` and add

```
<Directory "/path/to/public">
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>
```

So it might look something like this

```
DocumentRoot /path/to/public
<Directory "/path/to/public">
    # allow from all
    # Options None
    # Require all granted

    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>

# If you want to use multiple PHP version in one server
<FilesMatch \.php$>
    # For Apache version 2.4.10 and above, use SetHandler to run PHP as a fastCGI process server
    SetHandler "proxy:unix:/run/php/php7.4-fpm.sock|fcgi://localhost"
</FilesMatch>

ServerName domain-name.com
```