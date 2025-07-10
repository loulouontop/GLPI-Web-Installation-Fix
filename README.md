# 🛠️ GLPI 11.0.0-beta7 – Missing .htaccess Fix for Web Installer (Debian)

## 🔍 Problem

During a fresh installation of **GLPI 11.0.0-beta7** on a **Debian 12 Apache/PHP 8.2 server**, the web interface loads the initial installer page (`http://glpi.local/`) correctly.

However, when clicking on **"Go to install page"**, the browser either stays on the same page or shows a **blank/error screen**.  
This prevents installation from proceeding.

## ❗ Root Cause

GLPI 11 (based on Symfony) requires URL routing via `/public/index.php`, but the default archive **does not include the `.htaccess` file** in the `/public/` directory, which is essential for proper request redirection in Apache.

Previous stable versions (GLPI 10.x) **included this file**, which explains why this issue is new.

---

## ✅ Solution (Manual Fix)

Create the missing `.htaccess` file in `/public/` and apply correct permissions.

### 1. Create the `.htaccess` file

```bash
sudo nano /var/www/html/glpi/public/.htaccess
```

Paste the following content:

```apache
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [QSA,L]
</IfModule>
```

### 2. Apply proper permissions

```bash
sudo chown www-data:www-data /var/www/html/glpi/public/.htaccess
sudo chmod 644 /var/www/html/glpi/public/.htaccess
```

### 3. Restart Apache

```bash
sudo systemctl restart apache2
```

After these steps, reloading `http://glpi.local/` and clicking **"Go to install page"** launches the installer successfully ✅

---

## 💬 Community Suggestion

It is recommended that GLPI developers:

- **Re-add the `.htaccess` file** to the `/public` directory in future beta/stable releases
- Or **document this requirement** in the official installation guide for Apache users

---

## 👨‍💻 Tested Environment

| Component     | Version               |
|---------------|------------------------|
| OS            | Debian 12              |
| Web Server    | Apache 2.4.62          |
| PHP           | 8.2                    |
| MariaDB       | 10.11                  |
| GLPI          | 11.0.0-beta7 (GitHub)  |

---

## 🙌 Credits

Special thanks to **Mr. Larbi OUALI** ([GitHub profile](https://github.com/babydunet)),  
Help Desk Manager at Algérie Télécom, for his technical supervision and valuable insights during all the project.

---

## 🧠 Author

**Lokmane Benaziza** 🇩🇿  
IT Support Apprentice | Algérie Télécom  
GLPI Contributor & Technical Documentation Writer  
[LinkedIn](https://www.linkedin.com/in/benazizalokmane/)
