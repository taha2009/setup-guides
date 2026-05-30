# Increase Upload Size Limits

To allow large file uploads (e.g., 100MB), you must update limits in **both PHP and Apache** — each can independently reject the upload.

---

## 1. Set PHP upload limits

```bash
sudo nano /etc/php/*/apache2/php.ini
```

```ini
upload_max_filesize = 100M
post_max_size = 100M
max_execution_time = 300
max_input_time = 300
memory_limit = 256M
```

---

## 2. Set Apache request size limit

```bash
sudo nano /etc/apache2/apache2.conf
```

Add at the bottom:

```apache
LimitRequestBody 104857600
```

(100 × 1024 × 1024 = 104857600 bytes)

---

## 3. Restart Apache

```bash
sudo systemctl restart apache2
```

---

## 4. Verify

```bash
sudo nano /var/www/html/app/upload_test.php
```

```php
<?php phpinfo(); ?>
```

Open in browser and confirm `upload_max_filesize` and `post_max_size` both show `100M`.

**Delete the file after verifying:**

```bash
sudo rm /var/www/html/app/upload_test.php
```

---

## Why both layers must be configured

| Layer          | What it blocks                   |
|----------------|----------------------------------|
| Apache         | Drops request before PHP sees it |
| PHP            | Rejects the upload               |
| `memory_limit` | Needed to process the file in memory |

All three must allow the upload size.
