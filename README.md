
Step To Prepare Server For Laravel
----

1. Setup DNS
2. Setup Subdomain if any
3. Install `zip, git, update, nvm, composer, bower`
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

Install PHP 7.4
---

`sudo apt install php php-cli php-fpm php-json php-common php-mysql php-zip php-gd php-mbstring php-curl php-xml php-pear php-bcmath`

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
