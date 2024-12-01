# **MariaDB: Enabling Case Sensitivity for Database Names**

This guide explains how to enable case sensitivity for database names in MariaDB. By default, MariaDB's behavior regarding case sensitivity for database and table names is determined by the operating system. You can override this behavior by configuring the `lower_case_table_names` setting.

---

## **Why Enable Case Sensitivity?**

Enabling case sensitivity for database names is useful in scenarios where:
- You are importing/exporting databases between case-sensitive and case-insensitive environments.
- You want to adhere to naming conventions or require specific case-sensitive naming.

---

## **Configuration Based on Operating System**

### **Linux**

On Linux, file systems are typically case-sensitive by default. To enable case sensitivity for MariaDB database names:

1. **Edit the Configuration File**:
   Open `/etc/my.cnf` or `/etc/mysql/my.cnf` with a text editor:
   ```bash
   sudo nano /etc/my.cnf
   ```
   Add or update the following under the `[mysqld]` section:
   ```ini
   [mysqld]
   lower_case_table_names = 0
   ```

   - **Value Explanation**:
     - `0`: Case sensitivity is enabled. Database and table names are stored and compared as is.
     - `1`: Case sensitivity is disabled. Names are stored in lowercase.
     - `2`: Names are stored as declared but compared in lowercase (not typically used on Linux).

2. **Restart MariaDB**:
   Restart the MariaDB service to apply changes:
   ```bash
   sudo service mariadb restart
   ```
   or
   ```bash
   sudo systemctl restart mariadb
   ```

---

### **Windows**

On Windows, file systems are case-insensitive by default. To enable case sensitivity for MariaDB database names:

1. **Edit the Configuration File**:
   Open `C:\Program Files\MariaDB <version>\data\my.ini` with a text editor.
   Add or update the following under the `[mysqld]` section:
   ```ini
   [mysqld]
   lower_case_table_names = 2
   ```

   - **Value Explanation**:
     - `2`: Names are stored as declared but compared in lowercase.

2. **Restart MariaDB**:
   Restart MariaDB using the Command Prompt:
   ```cmd
   net stop MariaDB
   net start MariaDB
   ```
   or combine the commands:
   ```cmd
   net stop MariaDB && net start MariaDB
   ```

---

## **Important Notes**

1. **Impact on Existing Databases**:
   - Changing the `lower_case_table_names` setting may cause issues with existing databases, especially during migrations between environments with different case sensitivity configurations.
   - Ensure that database and table names comply with the new setting before making changes.

2. **Cross-Platform Compatibility**:
   - If you need cross-platform compatibility, consider using lowercase database and table names consistently.

3. **Backup Your Data**:
   - Always back up your databases before making configuration changes.

4. **Verify Configuration**:
   - After restarting MariaDB, verify the setting by logging into the MariaDB console and running:
     ```sql
     SHOW VARIABLES LIKE 'lower_case_table_names';
     ```

---

## **Conclusion**

Enabling case sensitivity for database names in MariaDB requires updating the `lower_case_table_names` setting in the configuration file. Be cautious when migrating databases between different environments, and always back up your data before making changes.