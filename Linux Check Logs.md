# **Checking Important Security Logs on a Debian Server**

Security logs are essential for monitoring, diagnosing, and investigating unauthorized access or system issues on a Linux server. Debian and its derivatives store logs in the `/var/log/` directory. This document covers the key logs you should monitor and how to analyze them effectively.

---

## **Table of Contents**

1. [Common Security Logs in Debian](#1-common-security-logs-in-debian)
2. [Commands to View Logs](#2-commands-to-view-logs)
3. [Important Logs and Their Purpose](#3-important-logs-and-their-purpose)
4. [Analyzing Logs with Tools](#4-analyzing-logs-with-tools)
5. [Best Practices for Log Management](#5-best-practices-for-log-management)

---

## **1. Common Security Logs in Debian**

### **Location of Logs**
The primary location for logs is:
```bash
/var/log/
```

### **Key Security Logs**
- **Authentication Logs**: `/var/log/auth.log`
- **System Logs**: `/var/log/syslog`
- **Web Server Logs**: Typically located in `/var/log/apache2/` or `/var/log/nginx/`
- **Kernel Logs**: `/var/log/kern.log`
- **Firewall Logs**: Managed by `iptables` or `ufw`, often in `/var/log/ufw.log`
- **Access Logs**: Application-specific, e.g., `/var/log/apache2/access.log`

---

## **2. Commands to View Logs**

### **Viewing Logs in Real-Time**
```bash
tail -f /var/log/auth.log
```

### **Search for Specific Terms**
```bash
grep "Failed password" /var/log/auth.log
```

### **Display Logs with Line Numbers**
```bash
cat -n /var/log/auth.log
```

### **View Logs with Less**
```bash
less /var/log/syslog
```

### **Analyze Logs from a Specific Time Period**
Use `journalctl` for systemd-based logs:
```bash
journalctl --since "2023-11-01" --until "2023-11-30"
```

---

## **3. Important Logs and Their Purpose**

### **1. Authentication Logs: `/var/log/auth.log`**
- Records login attempts and authentication events.
- Useful for tracking failed login attempts, `sudo` usage, and SSH activity.

#### Example: Check Failed SSH Login Attempts
```bash
grep "Failed password" /var/log/auth.log
```

#### Output Example:
```plaintext
Nov 30 12:34:56 server sshd[12345]: Failed password for invalid user admin from 192.168.1.10 port 2222 ssh2
```

---

### **2. System Logs: `/var/log/syslog`**
- Contains general system activity logs.
- Use this to identify issues related to services, hardware, and other processes.

#### Example: Check Service Start/Stop Events
```bash
grep "service-name" /var/log/syslog
```

---

### **3. Kernel Logs: `/var/log/kern.log`**
- Records kernel-level events.
- Critical for diagnosing hardware issues or kernel vulnerabilities.

#### Example: Check for Disk Errors
```bash
grep "disk" /var/log/kern.log
```

---

### **4. Web Server Logs**
#### **Apache Logs**
- **Access Log**: `/var/log/apache2/access.log`
  - Tracks all HTTP requests.
- **Error Log**: `/var/log/apache2/error.log`
  - Records errors encountered by the web server.

#### **Nginx Logs**
- **Access Log**: `/var/log/nginx/access.log`
- **Error Log**: `/var/log/nginx/error.log`

#### Example: View All HTTP 404 Errors
```bash
grep " 404 " /var/log/apache2/access.log
```

---

### **5. Firewall Logs: `/var/log/ufw.log`**
- Records incoming and outgoing connections filtered by the firewall.
- Analyze to detect unauthorized access attempts.

#### Example: Check for Dropped Connections
```bash
grep "DROP" /var/log/ufw.log
```

---

## **4. Analyzing Logs with Tools**

### **1. Use `GoAccess` for Web Logs**
- A real-time web log analyzer.
```bash
sudo apt install goaccess
goaccess /var/log/apache2/access.log -o report.html
```
- Open `report.html` in your browser for detailed insights.

---

### **2. Use `fail2ban` for Security**
- Automatically blocks IPs with repeated failed login attempts.

#### Example: View `fail2ban` Log
```bash
sudo cat /var/log/fail2ban.log
```

---

### **3. Visualize Logs with `ELK Stack`**
- Elasticsearch, Logstash, and Kibana for log aggregation and visualization.

---

## **5. Best Practices for Log Management**

### **1. Rotate Logs**
- Prevent logs from consuming excessive disk space by using `logrotate`.
```bash
sudo nano /etc/logrotate.d/apache2
```

### **2. Secure Logs**
- Restrict access to sensitive logs:
```bash
sudo chmod 640 /var/log/auth.log
```

### **3. Automate Monitoring**
- Use tools like `Splunk`, `Graylog`, or `Prometheus` for automated log analysis and alerts.

### **4. Archive Old Logs**
- Compress and archive logs to save disk space:
```bash
tar -czf logs-archive.tar.gz /var/log/
```

### **5. Regularly Review Logs**
- Schedule periodic checks of critical logs for anomalies.

### **6. Accepted Authentication Logs**
In addition to failed logins, it's essential to check **accepted logins** to verify successful access events.

#### Example: Check Accepted SSH Login Attempts
```bash
grep "Accepted" /var/log/auth.log
```

#### Output Example:
```plaintext
Nov 30 12:45:21 server sshd[56789]: Accepted password for john from 203.0.113.45 port 55232 ssh2
```

This output indicates:
- The username (`john`) successfully logged in.
- The IP address (`203.0.113.45`) of the connecting user.
- The port (`55232`) used for the SSH connection.

---

### **7. Checking Archived Logs (`.gz` Files)**

When logs are rotated or archived, they are often compressed as `.gz` files. You can analyze these files without uncompressing them.

#### View Archived Logs
```bash
zcat /var/log/auth.log.1.gz | grep "Accepted"
```

#### Search Within Archived Logs
```bash
zgrep "Accepted" /var/log/auth.log.1.gz
```

- **`zcat`**: Reads the contents of `.gz` files.
- **`zgrep`**: Performs `grep` directly on `.gz` files.

#### List Archived Log Files
```bash
ls /var/log/*.gz
```

---

### **Combine Real-Time and Archived Log Searches**
You can combine live and archived log searches to get a complete history:
```bash
grep "Accepted" /var/log/auth.log
cat /var/log/auth.log.1 | grep "Accepted"
zgrep "Accepted" /var/log/auth.log.*.gz
```

---

## **Best Practice: Periodic Review of Accepted Logs**
Regularly reviewing both accepted and failed authentication attempts helps:
1. **Identify Authorized Access**: Verify that accepted logins match expected users and IP addresses.
2. **Detect Suspicious Patterns**: Unusual times, locations, or frequent successful attempts may indicate compromised credentials.

### Example Automation
Use `cron` to automate log reviews:
```bash
crontab -e
```

Add a task to summarize login events daily:
```cron
@daily grep "Accepted" /var/log/auth.log | mail -s "Daily Login Report" your-email@example.com
```

---

This addition ensures a complete guide for checking both real-time and archived logs for failed and successful login attempts. Let me know if there's anything else you'd like to expand!