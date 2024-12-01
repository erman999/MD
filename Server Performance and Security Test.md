## **1. Sunucu Performansını Test Etmek**

Performans testi, sunucunuzun yük altında nasıl davrandığını anlamak ve trafik kapasitesini ölçmek için kritik öneme sahiptir.

### **1.1 Performans Test Araçları**

#### **a) Apache Benchmark (ab)**
Apache Benchmark, hızlı ve temel yük testleri için kullanılır.

- **Kurulum**:
  ```bash
  sudo apt install apache2-utils
  ```

- **Kullanım**:
  ```bash
  ab -n 1000 -c 10 http://yourdomain.com/
  ```
  - `-n`: Toplam istek sayısı.
  - `-c`: Eşzamanlı istek sayısı.

#### **b) Siege**
Siege, eşzamanlı kullanıcı yüklerini simüle ederek performansı analiz eder.

- **Kurulum**:
  ```bash
  sudo apt install siege
  ```

- **Kullanım**:
  ```bash
  siege -c 50 -t 1M http://yourdomain.com/
  ```
  - `-c`: Eşzamanlı kullanıcı sayısı.
  - `-t`: Test süresi (örneğin, 1 dakika).

#### **c) Locust**
Locust, Python tabanlı dağıtılmış yük testleri için güçlü bir araçtır.

- **Kurulum**:
  ```bash
  pip install locust
  ```

- **Kullanım**:
  ```bash
  locust -f locustfile.py
  ```
  - `locustfile.py` içinde test senaryolarınızı yazabilirsiniz.

#### **d) Çevrimiçi Araçlar**
- **Pingdom** ve **GTmetrix** gibi araçlar, web sitenizin yüklenme hızını ve performansını ölçer.

---

## **2. Saldırı Yöntemleri ve Araçları**

Saldırganların kullandığı yöntemleri anlamak, savunma stratejilerinizi geliştirmek için önemlidir.

### **2.1 DDoS ve Flood Saldırıları**
- **Amaç**: Trafiği tıkayarak sunucuyu çalışamaz hale getirmek.
- **Araçlar**:
  - **LOIC (Low Orbit Ion Cannon)**: DDoS saldırıları için kullanılan basit bir araç.
  - **hping3**: Komut satırı üzerinden SYN flood saldırıları düzenler.
    ```bash
    hping3 -S -p 80 --flood yourserver.com
    ```

### **2.2 Brute Force ve Login Saldırıları**
- **Amaç**: Şifre tahmini ile sisteme yetkisiz erişim sağlamak.
- **Araçlar**:
  - **Hydra**:
    ```bash
    hydra -l admin -P passwords.txt yourserver.com ssh
    ```

### **2.3 Zafiyet Taraması**
- **Amaç**: Sistem açıklarını tespit etmek.
- **Araçlar**:
  - **Metasploit**: Güvenlik açıklarını bulur ve sömürür.
  - **Nikto**: Web sunucularındaki zayıflıkları tarar.

---

## **3. Saldırıları Tespit Etmek**

### **3.1 Log Analizi**
Loglar, saldırıları anlamak ve kaynağını tespit etmek için en önemli araçtır.

- **Erişim Logları**:
  - Apache: `/var/log/apache2/access.log`
  - Nginx: `/var/log/nginx/access.log`
- **SSH Logları**:
  - `/var/log/auth.log`

**Örnek Komut**:
```bash
grep "Failed password" /var/log/auth.log
```

### **3.2 Ağ Trafiği İzleme**
- **Wireshark**: Ağ trafiğini detaylı şekilde izler.
- **Netstat**:
  ```bash
  netstat -an | grep :80
  ```

### **3.3 Anomali Tespiti**
- **GoAccess**: Gerçek zamanlı log analizi yapar.
  ```bash
  goaccess /var/log/nginx/access.log --log-format=COMBINED
  ```

---

## **4. Güvenlik Önlemleri**

### **4.1 Güvenlik Duvarı Kullanımı**
**iptables** veya **ufw** ile IP adreslerini engelleyebilirsiniz.
```bash
sudo ufw enable
sudo ufw deny from <malicious-ip>
```

### **4.2 Trafik Sınırlama**
- **Fail2Ban**:
  Kaba kuvvet saldırılarını önlemek için kullanılır.
  ```bash
  sudo apt install fail2ban
  ```
  **Ayar Dosyası** (`/etc/fail2ban/jail.local`):
  ```bash
  [ssh]
  enabled = true
  port = ssh
  logpath = /var/log/auth.log
  maxretry = 3
  bantime = 600
  ```

- **Nginx Rate Limiting**:
  ```nginx
  limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s;
  ```

### **4.3 Yazılım Güncellemeleri**
Sistem yazılımlarınızı ve bağımlılıkları düzenli olarak güncelleyin:
```bash
sudo apt update && sudo apt upgrade
```

---

## **5. İzleme ve Uyarı Sistemleri**

### **5.1 Monitoring Araçları**
- **Zabbix** veya **Nagios** gibi araçlarla sistem durumunu izleyin.
- Anlık uyarılar için alarmlar tanımlayın.

### **5.2 Log Tabanlı Uyarılar**
Anormal giriş denemeleri veya trafik için e-posta uyarıları ayarlayın.

---

## **Sonuç**

Bu dökümanla sunucu performansınızı test edebilir, potansiyel saldırılara karşı savunma önlemleri alabilir ve sisteminizi güvenli hale getirebilirsiniz. 

**Özet Adımlar**:
1. Performans testi araçlarıyla sunucunuzu analiz edin.
2. Logları ve ağ trafiğini düzenli olarak izleyin.
3. Güvenlik duvarı ve rate limiting yöntemleri ile saldırıları engelleyin.
4. Güncel yazılım ve izleme araçları ile sürekli güvenliği sağlayın.
