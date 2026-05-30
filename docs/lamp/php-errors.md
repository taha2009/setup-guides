# Enable PHP Error Display in Browser (Dev Only)

> **Warning:** Only do this in local/development environments. Never enable error display in production — it leaks file paths, SQL queries, secrets, and server layout.

---

## 1. Edit php.ini

```bash
sudo nano /etc/php/*/apache2/php.ini
```

(`*` = your PHP version, e.g. `8.1`, `8.2`)

Find and set:

```ini
display_errors = On
display_startup_errors = On
error_reporting = E_ALL
log_errors = On
```

---

## 2. Restart Apache

```bash
sudo systemctl restart apache2
```

---

## 3. Test

```bash
sudo nano /var/www/html/app/error.php
```

```php
<?php
echo $undefined_variable;
```

Open `http://<server-ip>/error.php`. You should see:

```
Notice: Undefined variable: undefined_variable in /var/www/html/app/error.php on line 2
```

---

## 4. Show fatal errors (optional)

Some PHP configs suppress fatal errors in Apache. To force them:

```bash
sudo nano /etc/php/*/apache2/php.ini
```

```ini
html_errors = On
```

---

## 5. Laravel / framework note

Also set in `.env`:

```
APP_DEBUG=true
```
