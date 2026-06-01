# OJS Implementation Plan for IJMT

## Context

Setting up Open Journal Systems (OJS 3.x) for IJMT (Indian Journal of Multidisciplinary Technology — or similar) under the IAMT umbrella. The journal is currently inactive and needs a structured submission/peer-review/publishing workflow to restart operations. Local deployment is done first to learn the interface and configure settings without risk; then the same config is replicated on the production VPS.

**User profile:** Windows laptop (local), non-technical but AI-assisted, owns `iamt.co.in` with DNS access.

---

## Services Chosen

| Service | Choice | Cost |
|---|---|---|
| VPS | Hostinger KVM 2 | ₹929/month |
| OS | Ubuntu 22.04 LTS | free |
| Journal software | OJS 3.4.x (latest stable) | free |
| SSL | Let's Encrypt via Certbot | free |
| Email / SMTP | Gmail SMTP (App Password) | free |
| Local dev | WSL2 (Ubuntu on Windows) + LAMP | free |

---

## Phase 1: Local Deployment (Windows via WSL2)

**Goal:** Run OJS locally on Windows using WSL2 (Ubuntu), learn the admin interface before touching production. WSL2 gives you a real Linux environment — the commands here are nearly identical to the VPS setup, so you'll be familiar by the time you deploy remotely.

> **Why WSL2 and not XAMPP/WAMP?** OJS is built for Linux/Apache. XAMPP on Windows causes PHP extension and file permission issues that are hard to debug. WSL2 runs a real Ubuntu instance and avoids all of that.

### Step 1 — Enable WSL2 and install Ubuntu

Open **PowerShell as Administrator** and run:

```powershell
wsl --install
```

This installs WSL2 + Ubuntu 22.04 by default. **Restart your laptop** when prompted.

After restart, Ubuntu will open and ask you to create a Unix username and password (this is separate from your Windows login — keep it simple, e.g. `ajeant`).

Verify it worked:
```powershell
wsl --list --verbose
# Should show Ubuntu with VERSION 2
```

### Step 2 — Install LAMP stack inside WSL2 (Ubuntu)

All remaining commands run inside the **Ubuntu terminal** (not PowerShell):

```bash
sudo apt update && sudo apt upgrade -y

# PHP 8.2 + required extensions
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt update
sudo apt install -y php8.2 php8.2-cli php8.2-mysql php8.2-xml \
  php8.2-mbstring php8.2-curl php8.2-zip php8.2-gd php8.2-intl \
  libapache2-mod-php8.2

# Apache
sudo apt install -y apache2
sudo a2enmod rewrite php8.2
sudo service apache2 start   # WSL2 uses 'service', not 'systemctl'

# MySQL
sudo apt install -y mysql-server
sudo service mysql start
sudo mysql_secure_installation
```

> **WSL2 note:** Services don't auto-start on reboot. Each time you open Ubuntu, run `sudo service apache2 start && sudo service mysql start` before accessing OJS.

### Step 3 — Create local OJS database

```bash
sudo mysql -u root -p <<'SQL'
CREATE DATABASE ojs_local CHARACTER SET utf8mb3;
CREATE USER 'ojs_user'@'localhost' IDENTIFIED BY 'ojspass';
GRANT ALL PRIVILEGES ON ojs_local.* TO 'ojs_user'@'localhost';
FLUSH PRIVILEGES;
SQL
```

### Step 4 — Download and extract OJS

```bash
cd /var/www
sudo wget https://pkp.sfu.ca/ojs/download/ojs-3.4.0-5.tar.gz
sudo tar -xzf ojs-3.4.0-5.tar.gz
sudo mv ojs-3.4.0-5 ojs-local
sudo chown -R www-data:www-data /var/www/ojs-local

# Files directory outside webroot
sudo mkdir -p /var/www/ojs_files_local
sudo chown -R www-data:www-data /var/www/ojs_files_local
```

### Step 5 — Apache config for localhost

```bash
sudo nano /etc/apache2/sites-available/ojs-local.conf
```

Paste:

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

Save with `Ctrl+O`, `Enter`, `Ctrl+X`, then:

```bash
sudo a2ensite ojs-local.conf
sudo a2dissite 000-default.conf
sudo service apache2 reload
```

Open **Windows browser** (Chrome/Edge) and go to: `http://localhost`

### Step 6 — Web Installer

Fill in:
- **Database host:** `localhost`
- **Database name:** `ojs_local`
- **Database user:** `ojs_user`
- **Database password:** `ojspass`
- **Files directory:** `/var/www/ojs_files_local`
- **Admin username:** choose (e.g. `admin`)
- **Admin password:** strong password
- **Admin email:** your email

### Step 7 — Configure journal locally (learn the interface)

In the OJS admin panel:
1. Create journal → name it **IJMT**, set abbreviation
2. Set journal description, ISSN (if you have one)
3. Upload journal logo
4. Create sections: `Research Articles`, `Review Articles`, `Case Studies`
5. Set up editorial roles: add yourself as Journal Manager + Editor
6. Review default email templates (Settings → Workflow → Emails)
7. Configure submission checklist (Settings → Workflow → Submission)
8. Add editorial board members (users → enroll as reviewers)

> To reset and start fresh locally: run `sudo mysql -u root -e "DROP DATABASE ojs_local; CREATE DATABASE ojs_local CHARACTER SET utf8mb3;"` inside Ubuntu, then `sudo rm -rf /var/www/ojs_files_local/*`.

---

## Phase 2: VPS Deployment (Hostinger KVM 2)

### Step 1 — Purchase VPS

1. Go to Hostinger → VPS → KVM 2 plan
2. Select **Ubuntu 22.04 LTS** as OS
3. Set a **root password** (or add SSH key — recommended)
4. Note the VPS IP address after provisioning (e.g. `123.456.78.90`)

### Step 2 — Point DNS subdomain

In your domain registrar's DNS panel (iamt.co.in):

```
Type: A
Name: journal
Value: <your VPS IP>
TTL: 300
```

After saving, propagation takes 10–60 minutes. Verify:
```bash
nslookup journal.iamt.co.in
```

### Step 3 — Initial server hardening (SSH in)

```bash
ssh root@<VPS_IP>

# Update system
apt update && apt upgrade -y

# Create non-root user (optional but good practice)
adduser deploy
usermod -aG sudo deploy

# Set timezone
timedatectl set-timezone Asia/Kolkata
```

### Step 4 — Install LAMP stack

```bash
# PHP 8.2 + required extensions
apt install -y software-properties-common
add-apt-repository ppa:ondrej/php
apt update
apt install -y php8.2 php8.2-cli php8.2-fpm php8.2-mysql php8.2-xml \
  php8.2-mbstring php8.2-curl php8.2-zip php8.2-gd php8.2-intl \
  php8.2-json libapache2-mod-php8.2

# Apache
apt install -y apache2
a2enmod rewrite php8.2
systemctl enable apache2

# MySQL 8
apt install -y mysql-server
mysql_secure_installation   # follow prompts: set root password, remove anon users

# Create OJS database
mysql -u root -p <<'SQL'
CREATE DATABASE ojs_db CHARACTER SET utf8mb3;
CREATE USER 'ojs_user'@'localhost' IDENTIFIED BY 'StrongPass123!';
GRANT ALL PRIVILEGES ON ojs_db.* TO 'ojs_user'@'localhost';
FLUSH PRIVILEGES;
SQL

# Tune PHP for OJS (file uploads, memory)
sed -i 's/upload_max_filesize = .*/upload_max_filesize = 50M/' /etc/php/8.2/apache2/php.ini
sed -i 's/post_max_size = .*/post_max_size = 55M/' /etc/php/8.2/apache2/php.ini
sed -i 's/memory_limit = .*/memory_limit = 256M/' /etc/php/8.2/apache2/php.ini
sed -i 's/max_execution_time = .*/max_execution_time = 300/' /etc/php/8.2/apache2/php.ini
```

### Step 5 — Download and extract OJS

```bash
cd /var/www

# Download latest OJS 3.4.x (check https://pkp.sfu.ca/software/ojs/download/ for latest URL)
wget https://pkp.sfu.ca/ojs/download/ojs-3.4.0-5.tar.gz
tar -xzf ojs-3.4.0-5.tar.gz
mv ojs-3.4.0-5 ijmt
chown -R www-data:www-data /var/www/ijmt
chmod -R 755 /var/www/ijmt

# Create uploads directory outside webroot
mkdir -p /var/www/ojs_files
chown -R www-data:www-data /var/www/ojs_files
```

### Step 6 — Apache Virtual Host

Create `/etc/apache2/sites-available/journal.iamt.co.in.conf`:

```apache
<VirtualHost *:80>
    ServerName journal.iamt.co.in
    DocumentRoot /var/www/ijmt

    <Directory /var/www/ijmt>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/ojs_error.log
    CustomLog ${APACHE_LOG_DIR}/ojs_access.log combined
</VirtualHost>
```

```bash
a2ensite journal.iamt.co.in.conf
a2dissite 000-default.conf
systemctl reload apache2
```

### Step 7 — SSL with Let's Encrypt

```bash
apt install -y certbot python3-certbot-apache
certbot --apache -d journal.iamt.co.in

# Test auto-renewal
certbot renew --dry-run
```

### Step 8 — OJS Web Installer

Open `https://journal.iamt.co.in` in browser.

Fill in:
- **Database driver:** `MySQLi`
- **Host:** `localhost`
- **Database name:** `ojs_db`
- **Username:** `ojs_user`
- **Password:** `StrongPass123!`
- **Files directory:** `/var/www/ojs_files`
- **Admin username/password/email:** (same as local setup)

### Step 9 — Configure Gmail SMTP

1. In Gmail → Account → Security → enable **2-Step Verification**
2. Then create an **App Password** (select "Mail" + "Other device" = OJS)
3. Copy the 16-character App Password

In OJS admin panel → Site Settings → Bulk Emails → SMTP:
```
SMTP Server:       smtp.gmail.com
SMTP Port:         465
SMTP Encryption:   SSL/TLS
SMTP Username:     youremail@gmail.com
SMTP Password:     <16-char App Password>
```

Send a test email to verify.

### Step 10 — Set up cron job (critical for OJS)

```bash
crontab -e -u www-data
```

Add:
```
0 * * * * php /var/www/ijmt/tools/runScheduledTasks.php /var/www/ijmt/lib/pkp/xml/scheduledTasks.xml
```

This handles: automated reminders to reviewers, stats aggregation, notification queues.

### Step 11 — Automatic backups

```bash
# Create backup script
cat > /usr/local/bin/ojs-backup.sh <<'EOF'
#!/bin/bash
DATE=$(date +%Y%m%d)
BACKUP_DIR="/root/ojs-backups"
mkdir -p $BACKUP_DIR
mysqldump -u ojs_user -pStrongPass123! ojs_db | gzip > $BACKUP_DIR/db_$DATE.sql.gz
tar -czf $BACKUP_DIR/files_$DATE.tar.gz /var/www/ojs_files
# Keep last 14 days only
find $BACKUP_DIR -mtime +14 -delete
EOF

chmod +x /usr/local/bin/ojs-backup.sh

# Add to root crontab (daily at 2am)
(crontab -l 2>/dev/null; echo "0 2 * * * /usr/local/bin/ojs-backup.sh") | crontab -
```

---

## Phase 3: Post-Deployment Journal Configuration

These steps are done inside the OJS admin panel after the web installer:

1. **Journal identity** — Settings → Journal → Masthead: name, ISSN, description, logo
2. **Contact info** — Settings → Journal → Contact: principal + technical contact
3. **Sections** — Settings → Journal → Sections: create `Research Articles`, `Review Articles`
4. **Workflow** — Settings → Workflow → Submission: set up checklist, copyright statement
5. **Peer review** — Settings → Workflow → Review: set blind review type, deadlines (14 days default)
6. **Email templates** — Settings → Workflow → Emails: customize reviewer request email with IJMT branding
7. **Navigation** — appearance → Navigation: add links to main IAMT site
8. **Users** — Enroll editors, reviewers with appropriate roles
9. **Announcement** — make an announcement about IJMT accepting submissions

---

## Integration with iamt.co.in (Final Step)

On the main Vue.js/Vercel site, add these links:

```
Submit Article  →  https://journal.iamt.co.in/index.php/ijmt/about/submissions
Current Issue   →  https://journal.iamt.co.in/index.php/ijmt/issue/current
Archives        →  https://journal.iamt.co.in/index.php/ijmt/issue/archive
Editorial Board →  https://journal.iamt.co.in/index.php/ijmt/about/editorialTeam
```

---

## Verification Checklist

- [ ] WSL2: Ubuntu 22.04 installed and running (`wsl --list --verbose` shows VERSION 2)
- [ ] Local: Apache and MySQL services started inside WSL2
- [ ] Local: OJS loads at `http://localhost` in Windows browser, web installer completes
- [ ] Local: Can create a test journal and submit a dummy article end-to-end
- [ ] VPS: `https://journal.iamt.co.in` loads with valid SSL (green padlock)
- [ ] VPS: Can register as author and submit a test paper
- [ ] VPS: Editor receives email notification of submission (SMTP test)
- [ ] VPS: Cron job is running (`systemctl status cron`, check `/var/log/syslog`)
- [ ] VPS: Daily backup file appears in `/root/ojs-backups/`
- [ ] Domain: `journal.iamt.co.in` resolves to VPS IP (`nslookup journal.iamt.co.in`)
- [ ] Main site: "Submit Article" link routes correctly to OJS

---

## Total Cost Summary

| Item | Cost |
|---|---|
| Hostinger KVM 2 VPS | ₹929/month (~₹11,148/year) |
| Let's Encrypt SSL | Free |
| Gmail SMTP | Free |
| OJS software | Free |
| **Total ongoing** | **₹929/month** |

One-time only: ~30–60 minutes setup time (with AI assistance).
