# Installing the LAMP Stack (Ubuntu 22.04 / 24.04)

Production-grade installation of Linux + Apache + MySQL + PHP.

---

## 1. Update system

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 2. Install Apache

```bash
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl start apache2
```

Verify:

```bash
systemctl status apache2
```

Open `http://<your-server-ip>` — you should see the **Apache2 Ubuntu Default Page**.

---

## 3. Install MySQL

```bash
sudo apt install mysql-server -y
sudo mysql_secure_installation
```

Recommended answers:

```
VALIDATE PASSWORD: n
Remove anonymous users: y
Disallow root login remotely: y
Remove test database: y
Reload privilege tables: y
```

Test login:

```bash
sudo mysql
```

```sql
EXIT;
```

---

## 4. Install PHP and required modules

```bash
sudo apt install php libapache2-mod-php php-mysql php-cli php-curl php-zip php-mbstring php-xml php-gd -y
php -v
```

---

## 5. Configure Apache to prioritize PHP

```bash
sudo nano /etc/apache2/mods-enabled/dir.conf
```

```apache
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```

```bash
sudo systemctl restart apache2
```

---

## 6. Test PHP

```bash
sudo nano /var/www/html/info.php
```

```php
<?php phpinfo(); ?>
```

Open `http://<your-server-ip>/info.php`. If the PHP info page loads, LAMP is working.

**Remove it immediately after testing:**

```bash
sudo rm /var/www/html/info.php
```

---

## 7. Set web root permissions

```bash
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

---

## 8. Create a MySQL database and app user

```bash
sudo mysql
```

```sql
CREATE DATABASE appdb;
CREATE USER 'appuser'@'localhost' IDENTIFIED BY 'StrongPassword123!';
GRANT ALL PRIVILEGES ON appdb.* TO 'appuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## 9. Enable Apache rewrite module

Required for Laravel, WordPress, Symfony, etc.

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

---

## Optional: Install phpMyAdmin

```bash
sudo apt install phpmyadmin -y
sudo systemctl restart apache2
```

Access at `http://<server-ip>/phpmyadmin`.

---

## Final stack check

```bash
apache2 -v
mysql --version
php -v
```

---

**Next:** [Configure virtual host and enable .htaccess](./02-configure-vhost-htaccess.md)
