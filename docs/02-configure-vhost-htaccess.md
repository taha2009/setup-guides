# Configure Apache Virtual Host and Enable .htaccess

Apache ignores `.htaccess` by default (`AllowOverride None`), which breaks URL rewrites, redirects, and framework routing (Laravel, WordPress, Symfony, etc.). This guide configures the virtual host correctly.

---

## 1. Edit the virtual host config

```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```

**Default web root (`/var/www/html`):**

```apache
<VirtualHost *:80>
    DocumentRoot /var/www/html

    <Directory /var/www/html>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

**Custom web root (e.g., `/var/www/html/app`):**

First create the directory:

```bash
sudo mkdir -p /var/www/html/app
sudo chown -R www-data:www-data /var/www/html/app
sudo chmod -R 755 /var/www/html/app
```

Then configure:

```apache
<VirtualHost *:80>
    DocumentRoot /var/www/html/app

    <Directory /var/www/html/app>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Key settings:
- `AllowOverride All` — enables `.htaccess` processing
- `Require all granted` — allows access to the directory
- Always set this in the **virtual host file**, not just `apache2.conf` (vhost rules take precedence)

---

## 2. Enable the rewrite module and restart

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

---

## 3. Test .htaccess (optional)

```bash
sudo nano /var/www/html/app/.htaccess
```

```apache
RewriteEngine On
RewriteRule ^test$ index.php [L]
```

```bash
sudo nano /var/www/html/app/index.php
```

```php
<?php echo "Rewrite working"; ?>
```

Visit `http://<server-ip>/test` — you should see `Rewrite working`.

---

## Why setting this in apache2.conf alone doesn't work

Placing a `<Directory>` block in `apache2.conf` is not enough — Apache applies **virtual host rules first**. If the default vhost still has `AllowOverride None` (implicit), `.htaccess` is silently ignored even if `apache2.conf` says otherwise.

The fix is always in the virtual host file.

---

## Framework compatibility

| Framework   | Status |
|-------------|--------|
| Laravel     | ✔      |
| WordPress   | ✔      |
| Symfony     | ✔      |
| CodeIgniter | ✔      |

> For Laravel: point `DocumentRoot` to the `public` subdirectory (e.g., `/var/www/html/app/public`).
