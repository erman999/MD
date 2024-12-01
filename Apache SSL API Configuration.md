### Step 1: Modify `your-domain-le-ssl.conf`

This configuration file handles your HTTPS setup. Update it to include the reverse proxy settings for forwarding `/api` requests to your Node.js app.

1. Open the file for editing:
   ```bash
   sudo nano /etc/apache2/sites-available/your-domain-le-ssl.conf
   ```

2. Add the reverse proxy directives inside the `<VirtualHost *:443>` block:
   ```apache
   <IfModule mod_ssl.c>
   <VirtualHost *:443>
       ServerAdmin webmaster@localhost
       ServerName your-domain.com
       ServerAlias www.your-domain.com
       DocumentRoot /var/www/your-domain.com
       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
       RewriteEngine on

       # Proxy settings for Node.js API
       ProxyPass /api http://localhost:3000/api
       ProxyPassReverse /api http://localhost:3000/api

       Include /etc/letsencrypt/options-ssl-apache.conf
       SSLCertificateFile /etc/letsencrypt/live/your-domain.com/fullchain.pem
       SSLCertificateKeyFile /etc/letsencrypt/live/your-domain.com/privkey.pem
   </VirtualHost>
   </IfModule>
   ```

3. Save and exit the file.

---

### Step 2: Test and Reload Apache Configuration

1. Check the Apache configuration for syntax errors:
   ```bash
   sudo apache2ctl configtest
   ```

2. Reload Apache to apply the changes:
   ```bash
   sudo systemctl reload apache2
   ```

---

### Step 3: Ensure Node.js is Running Securely

1. Make sure your Node.js app is bound only to `localhost`:
   ```javascript
   app.listen(3000, '127.0.0.1', () => {
       console.log(`Node.js API is running on http://localhost:3000`);
   });
   ```

2. Start the Node.js app using a process manager like `pm2` for better reliability:
   ```bash
   npm install -g pm2
   pm2 start app.js --name node-api
   pm2 startup
   pm2 save
   ```

---

### Step 4: Test Your Setup

1. Visit your domain to check if the API works:
   ```bash
   curl https://your-domain.com/api/test
   ```

2. You should see the JSON response:
   ```json
   {"message": "Hello from Node.js API!"}
   ```

---

### Step 5: Security Enhancements

- Ensure port `3000` is not accessible externally:
  ```bash
  sudo ufw deny 3000
  ```

- Monitor your Node.js app with `pm2` logs:
  ```bash
  pm2 logs node-api
  ```
