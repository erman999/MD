# **Comprehensive Guide for Securing Express with CSRF and MariaDB Integration**

This guide outlines the steps to implement CSRF protection, session management with MariaDB, secure routing, and Apache configuration for logging. It excludes the use of ORMs like Sequelize, focusing on `mysql2` for database interactions.

---

## **1. Prerequisites**

### **Required Tools and Packages**
- **Express Framework**: For building the application.
- **mysql2**: To interact with a MariaDB database.
- **csurf**: For CSRF protection.
- **express-session**: For session management.
- **Helmet**: For securing HTTP headers.
- **Compression**: For reducing response size.
- **Apache**: To handle HTTPS, reverse proxying, and logging.

---

## **2. Setting Up Session Management**

Sessions are critical for tracking user state. We'll use `express-session` with MariaDB as the session store.

### **Install Required Packages**
```bash
npm install express-session mysql2
```

### **Configure MariaDB for Session Storage**

1. **Create a Session Table**: Use the following SQL to create a table for storing session data:
   ```sql
   CREATE TABLE sessions (
       session_id VARCHAR(255) PRIMARY KEY,
       expires INT NOT NULL,
       data TEXT
   );
   ```

2. **Configure Session Middleware**: Use `mysql2` to connect to the database and manage sessions.
   ```javascript
   const session = require('express-session');
   const mysql = require('mysql2');

   // Create a MariaDB connection
   const db = mysql.createConnection({
       host: process.env.DB_HOST,
       user: process.env.DB_USER,
       password: process.env.DB_PASSWORD,
       database: process.env.DB_NAME,
   });

   // Session Middleware
   app.use(
       session({
           secret: process.env.SESSION_SECRET,
           resave: false,
           saveUninitialized: false,
           store: new (require('express-mysql-session')(session))({}, db.promise()),
           cookie: {
               secure: process.env.NODE_ENV === 'production', // Enable HTTPS-only cookies in production
               httpOnly: true, // Prevent JavaScript access
               maxAge: 1800000, // 30 minutes
               sameSite: 'lax', // Protect against CSRF
           },
       })
   );
   ```

---

## **3. Implement CSRF Protection**

CSRF protection ensures that malicious actors cannot forge requests from authenticated users.

### **Install and Configure `csurf`**
```bash
npm install csurf
```

**Middleware Configuration:**
```javascript
const csrf = require('csurf');

// Apply CSRF Protection Middleware
app.use(csrf());

// Handle CSRF Errors Gracefully
app.use((err, req, res, next) => {
    if (err.code === 'EBADCSRFTOKEN') {
        return res.status(403).send('Invalid CSRF token');
    }
    next(err);
});
```

### **Include CSRF Tokens in Forms**

1. Pass the CSRF token to your views:
   ```javascript
   app.get('/form', (req, res) => {
       res.render('form', { csrfToken: req.csrfToken() });
   });
   ```

2. Add the token to your form as a hidden input field:
   ```html
   <form action="/submit" method="POST">
       <input type="hidden" name="_csrf" value="<%= csrfToken %>">
       <input type="text" name="data">
       <button type="submit">Submit</button>
   </form>
   ```

3. Secure AJAX requests by including the CSRF token in request headers.

---

## **4. Use Apache for Logging**

If your application is behind Apache, leverage its logging instead of implementing a Node.js logging library.

### **Apache Logging Configuration**
Add the following directives to your Apache configuration file:
```apache
CustomLog ${APACHE_LOG_DIR}/access.log combined
ErrorLog ${APACHE_LOG_DIR}/error.log
```

To analyze logs, use tools like `GoAccess`:
```bash
sudo apt install goaccess
goaccess /var/log/apache2/access.log -o report.html
```

---

## **5. Enhance Security and Performance**

### **1. Secure HTTP Headers with Helmet**
```bash
npm install helmet
```
Add Helmet to your app:
```javascript
const helmet = require('helmet');
app.use(helmet());
```

### **2. Compress Responses**
```bash
npm install compression
```
Use compression middleware:
```javascript
const compression = require('compression');
app.use(compression());
```

---

## **6. MariaDB CRUD Operations with `mysql2`**

Below is an example of how to perform basic database operations:

1. **Create a Connection:**
   ```javascript
   const mysql = require('mysql2');
   const db = mysql.createConnection({
       host: process.env.DB_HOST,
       user: process.env.DB_USER,
       password: process.env.DB_PASSWORD,
       database: process.env.DB_NAME,
   });
   ```

2. **Insert Data:**
   ```javascript
   db.query('INSERT INTO users (name, email) VALUES (?, ?)', ['John Doe', 'john@example.com'], (err, results) => {
       if (err) throw err;
       console.log('User added:', results.insertId);
   });
   ```

3. **Retrieve Data:**
   ```javascript
   db.query('SELECT * FROM users', (err, results) => {
       if (err) throw err;
       console.log('Users:', results);
   });
   ```

---

## **7. Exempt Routes from CSRF Protection**

To exempt specific routes (like public APIs) from CSRF protection:
```javascript
app.use((req, res, next) => {
    const exemptRoutes = ['/api/login', '/api/register'];
    if (exemptRoutes.includes(req.path)) return next();
    csrf()(req, res, next);
});
```

---

## **8. Test Your Implementation**

1. **Simulate CSRF Attacks**: Attempt to submit forms or send AJAX requests without valid tokens and verify that they are blocked.
2. **Validate Logs**: Check Apache logs to ensure all requests are logged appropriately.
3. **Verify Security Headers**: Use browser developer tools or tools like [SecurityHeaders](https://securityheaders.com/) to validate header configurations.

---

## **9. Best Practices**

- **Environment Variables**: Store sensitive information in `.env` files and load them using `dotenv`.
- **Regular Dependency Updates**: Run `npm outdated` and `npm update` frequently.
- **HTTPS Enforcement**: Always run your app behind an HTTPS proxy.
- **Session Secret Management**: Use long, randomly generated session secrets.

---

## **10. Conclusion**

This guide provides a robust foundation for securing your Express application with CSRF protection, session management using MariaDB, and leveraging Apache for logging and reverse proxying. By combining these techniques with regular monitoring and security updates, you can create a secure and scalable application.