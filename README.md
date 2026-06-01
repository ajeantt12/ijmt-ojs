# IJMT — OJS Local Setup (WSL2 on Windows)

This guide covers two things:
- **First-time installation** of OJS locally using WSL2 on Windows
- **How to restart** everything after a reboot (daily use)

---

## Every Time You Reboot — Start Services First

WSL2 does not auto-start services. Every time you open Ubuntu, run this before accessing OJS:

```bash
sudo service apache2 start
sudo service mysql start
```

Then open your Windows browser and go to: `http://localhost`

> If `http://localhost` still doesn't work after starting services, see the [Troubleshooting](#troubleshooting) section below.

---

## First-Time Installation

### Prerequisites (Windows, run once)

Open **PowerShell as Administrator**:

```powershell
wsl --install
```

Restart your laptop when prompted. After restart, Ubuntu opens and asks you to create a Unix username and password — keep it simple (e.g. `ajeant`). This is separate from your Windows login.

Verify WSL2 is set up:
```powershell
wsl --list --verbose
# Should show: Ubuntu  Stopped  2
```

To open Ubuntu any time:
```powershell
wsl
```

---

### Step 1 — Update Ubuntu

Run inside the **Ubuntu terminal**:

```bash
sudo apt update && sudo apt upgrade -y
```

---

### Step 2 — Install PHP 8.2

```bash
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt update
sudo apt install -y php8.2 php8.2-cli php8.2-mysql php8.2-xml \
  php8.2-mbstring php8.2-curl php8.2-zip php8.2-gd php8.2-intl \
  libapache2-mod-php8.2
```

---

### Step 3 — Install and start Apache

```bash
sudo apt install -y apache2
sudo a2enmod rewrite php8.2
sudo service apache2 start
```

---

### Step 4 — Install and configure MySQL

```bash
sudo apt install -y mysql-server
sudo service mysql start
```

Run the security setup:
```bash
sudo mysql_secure_installation
```

When prompted:
| Question | Answer |
|---|---|
| Set up VALIDATE PASSWORD component? | `y` |
| Password validation level | `0` (LOW is fine for local) |
| Remove anonymous users? | `y` |
| Disallow root login remotely? | `y` |
| Remove test database? | `y` |
| Reload privilege tables? | `y` |

> If the password validation blocks you later, see [Troubleshooting](#troubleshooting).

---

### Step 5 — Create the OJS database

```bash
sudo mysql -u root <<'SQL'
UNINSTALL COMPONENT 'file://component_validate_password';
DROP DATABASE IF EXISTS ojs_local;
CREATE DATABASE ojs_local CHARACTER SET utf8mb3;
CREATE USER IF NOT EXISTS 'ojs_user'@'localhost' IDENTIFIED BY 'ojspass';
GRANT ALL PRIVILEGES ON ojs_local.* TO 'ojs_user'@'localhost';
FLUSH PRIVILEGES;
SQL
```

> The `UNINSTALL COMPONENT` line disables strict password validation for local dev — fine locally, do **not** do this on the VPS.

Verify the user was created:
```bash
sudo mysql -u root -e "SELECT User, Host FROM mysql.user WHERE User='ojs_user';"
# Should show: ojs_user | localhost
```

---

### Step 6 — Download and extract OJS

```bash
cd /var/www
sudo wget https://pkp.sfu.ca/ojs/download/ojs-3.4.0-5.tar.gz
sudo tar -xzf ojs-3.4.0-5.tar.gz
sudo mv ojs-3.4.0-5 ojs-local
sudo chown -R www-data:www-data /var/www/ojs-local

# Files directory (outside webroot)
sudo mkdir -p /var/www/ojs_files_local
sudo chown -R www-data:www-data /var/www/ojs_files_local
```

---

### Step 7 — Configure Apache for localhost

```bash
sudo nano /etc/apache2/sites-available/ojs-local.conf
```

Paste exactly this:

```apache
<VirtualHost *:80>
    ServerName localhost
    DocumentRoot /var/www/ojs-local

    <Directory /var/www/ojs-local>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

Save: `Ctrl+O` → `Enter` → `Ctrl+X`

Then enable the site:
```bash
sudo a2ensite ojs-local.conf
sudo a2dissite 000-default.conf
sudo service apache2 reload
```

---

### Step 8 — Tune PHP settings

```bash
sudo sed -i 's/upload_max_filesize = .*/upload_max_filesize = 50M/' /etc/php/8.2/apache2/php.ini
sudo sed -i 's/post_max_size = .*/post_max_size = 55M/' /etc/php/8.2/apache2/php.ini
sudo sed -i 's/memory_limit = .*/memory_limit = 256M/' /etc/php/8.2/apache2/php.ini
sudo sed -i 's/max_execution_time = .*/max_execution_time = 300/' /etc/php/8.2/apache2/php.ini
sudo service apache2 reload
```

---

### Step 9 — Run the OJS web installer

Open your **Windows browser** (Chrome or Edge) and go to:

```
http://localhost
```

Fill in the installer form:

| Field | Value |
|---|---|
| Database driver | `MySQLi` |
| Database host | `localhost` |
| Database name | `ojs_local` |
| Database username | `ojs_user` |
| Database password | `ojspass` |
| Files directory | `/var/www/ojs_files_local` |
| Admin username | your choice (e.g. `admin`) |
| Admin password | something you'll remember |
| Admin email | your email |

Click **Install** and wait. When done, you'll be redirected to the OJS dashboard.

---

## Reset to a Clean Slate

If you want to wipe everything and start the installer fresh:

```bash
# Wipe and recreate the database
sudo mysql -u root -e "DROP DATABASE IF EXISTS ojs_local; CREATE DATABASE ojs_local CHARACTER SET utf8mb3;"

# Wipe uploaded files
sudo rm -rf /var/www/ojs_files_local/*

# Remove OJS config so the web installer runs again
sudo rm -f /var/www/ojs-local/config.inc.php
```

Then go to `http://localhost` and run through Step 9 again.

---

## Troubleshooting

### `http://localhost` shows "This page isn't working"

Apache may not be running. Check:
```bash
sudo service apache2 status
sudo service mysql status
```

If stopped, start them:
```bash
sudo service apache2 start
sudo service mysql start
```

Test Apache is serving from within WSL2:
```bash
curl http://localhost
```

If curl returns nothing or an error, check the Apache error log:
```bash
sudo tail -30 /var/log/apache2/error.log
```

---

### `http://localhost` shows a blank page after resetting

The `config.inc.php` from the old install is still there. Remove it:
```bash
sudo rm -f /var/www/ojs-local/config.inc.php
```

Then refresh `http://localhost` — the web installer should appear.

---

### MySQL password error: `does not satisfy current policy requirements`

The VALIDATE PASSWORD component is blocking the simple local password. Run:
```bash
sudo mysql -u root -e "UNINSTALL COMPONENT 'file://component_validate_password';"
```

Then re-run the database creation command from Step 5.

---

### Database already exists error

If you see `Can't create database 'ojs_local'; database exists`, run:
```bash
sudo mysql -u root -e "DROP DATABASE IF EXISTS ojs_local; CREATE DATABASE ojs_local CHARACTER SET utf8mb3;"
```

---

### Apache config check

Verify your site config is active and Apache sees it correctly:
```bash
ls /etc/apache2/sites-enabled/
# Should show: ojs-local.conf

sudo apache2ctl -S
# Should show: *:80  localhost  (/etc/apache2/sites-enabled/ojs-local.conf:1)
```

---

## Quick Reference

| Task | Command |
|---|---|
| Open Ubuntu from Windows | `wsl` in PowerShell |
| Start services after reboot | `sudo service apache2 start && sudo service mysql start` |
| Check Apache status | `sudo service apache2 status` |
| Check MySQL status | `sudo service mysql status` |
| View Apache error log | `sudo tail -30 /var/log/apache2/error.log` |
| Open MySQL shell | `sudo mysql -u root` |
| Full reset (wipe + reinstall) | See [Reset to a Clean Slate](#reset-to-a-clean-slate) |
| OJS local URL | `http://localhost` |
