# **Linux Utilities and Commands**

A collection of essential Linux commands and configurations for system administrators and developers. Updated for modern environments and tools.

---

## **Table of Contents**

1. [Creating and Extracting Archives](#1-creating-and-extracting-archives)
2. [MySQL/MariaDB Commands](#2-mysqlmariadb-commands)
3. [PHP Management](#3-php-management)
4. [FTP Configuration: vsftpd](#4-ftp-configuration-vsftpd)
5. [Certbot for SSL Certificates](#5-certbot-for-ssl-certificates)
6. [Remove `.php` Extension from URLs](#6-remove-php-extension-from-urls)
7. [Managing FLAC Audio with CUE](#7-managing-flac-audio-with-cue)
8. [Basic Linux Commands](#8-basic-linux-commands)

---

## **1. Creating and Extracting Archives**

### Create `tar.gz` Archive
```bash
tar cvzf archive.tar.gz /path/to/files
```

### Extract `tar.gz` Archive
```bash
tar -xvf archive.tar.gz
```

---

## **2. MySQL/MariaDB Commands**

### Insert Database
```bash
mysql -uUSERNAME -pPASSWORD DB_NAME < /path/to/MY_DB.sql
```

### Allow Remote Connection
1. **Grant Access to a Remote User**:
   ```sql
   GRANT ALL PRIVILEGES ON *.* TO 'user'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
   FLUSH PRIVILEGES;
   ```

2. **Update Configuration**:
   - Comment out `bind-address` in the configuration file (locations vary by distribution):
     ```ini
     # bind-address = 127.0.0.1
     ```

3. **Restart the MySQL Server**:
   ```bash
   sudo systemctl restart mysql
   ```

---

## **3. PHP Management**

### Restart PHP-FPM
```bash
sudo systemctl restart php8.1-fpm
```

### Update PHP Upload Settings
Edit `php.ini`:
```ini
post_max_size = 50M
upload_max_filesize = 50M
max_execution_time = 300
```

### Folder Permissions for File Uploads
```bash
sudo chown www-data:www-data /path/to/folder
sudo chmod 775 /path/to/folder
```

---

## **4. FTP Configuration: vsftpd**

### Example `vsftpd.conf` for Anonymous & User Login
Edit `/etc/vsftpd.conf`:
```ini
anonymous_enable=YES
anon_root=/var/ftp/
no_anon_password=YES
hide_ids=YES
pasv_min_port=40000
pasv_max_port=50000
```

Restart `vsftpd`:
```bash
sudo systemctl restart vsftpd
```

---

## **5. Certbot for SSL Certificates**

### Generate a New Certificate
```bash
sudo certbot certonly --standalone --preferred-challenges http -d example.com
```

### Renew All Certificates
```bash
sudo certbot renew
```

### Renew Without Stopping the Web Server
```bash
sudo certbot certonly --webroot -w /path/to/webroot -d example.com
```

### Check Certificate Status
```bash
sudo certbot certificates
```

### Delete a Certificate
```bash
sudo certbot delete --cert-name example.com
```

---

## **6. Remove `.php` Extension from URLs**

### Using `.htaccess`
```apache
RewriteEngine On
RewriteCond %{REQUEST_FILENAME}.php -f
RewriteRule !.*\.php$ %{REQUEST_FILENAME}.php [QSA,L]
```

### Using Virtual Host Configuration
```apache
<Directory /path/to/directory>
    AllowOverride All
    Options FollowSymLinks
    Require all granted

    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME}.php -f
    RewriteRule !.*\.php$ %{REQUEST_FILENAME}.php [QSA,L]
</Directory>
```

---

## **7. Managing FLAC Audio with CUE**

### Split FLAC Files with CUE
```bash
shnsplit -f file.cue -t %n-%t -o flac file.flac
```

### Alternative Method
```bash
cuebreakpoints file.cue | shnsplit -o flac file.flac
```

---

## **8. Basic Linux Commands**

### Hardware Information
1. **System Information**:
   ```bash
   uname -a
   ```
2. **CPU Information**:
   ```bash
   lscpu
   ```
3. **Hardware List**:
   ```bash
   lshw
   ```

4. **List PCI Devices**:
   ```bash
   lspci
   ```

---

### Disk and File System

1. **Disk Usage**:
   ```bash
   df -H
   ```
2. **Interactive Disk Usage Viewer**:
   ```bash
   sudo apt install ncdu
   ncdu
   ```

---

### Screen Command (Session Management)
1. **List Active Sessions**:
   ```bash
   screen -ls
   ```

2. **Create a New Session**:
   ```bash
   screen -S session_name
   ```

3. **Reconnect to a Session**:
   ```bash
   screen -r session_name
   ```

4. **Detach from a Session**:
   Press `Ctrl + A`, then `D`.

---

### Additional Notes

- **Certbot**:
  Ensure your Certbot installation is up to date:
  ```bash
  sudo apt update && sudo apt install certbot
  ```

- **PHP Versions**:
  Replace `php8.1-fpm` with the version you’re using.

- **MySQL/MariaDB**:
  Use MariaDB for better performance and compatibility with open-source applications.

- **System Monitoring**:
  Install tools like `htop`, `iotop`, and `glances` for enhanced system monitoring.
