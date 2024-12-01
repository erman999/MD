## **Apache Performans Optimizasyonu **

Apache, doğru yapılandırmalar ve modüllerle Nginx’e yakın hızda çalışabilir. Bu döküman, Apache’yi hızlandırmak ve performansını artırmak için izlenmesi gereken adımları detaylı bir şekilde açıklamaktadır.

---

### **1. Event MPM Modülünü Kullanma**
Apache’nin Multi-Processing Modülleri (MPM), bağlantıları yönetir. **Event MPM**, asenkron, olay tabanlı bir model sunar ve Nginx’e benzer performans sağlar.

#### **Adımlar:**
1. Öncelikle mevcut MPM modülünü devre dışı bırakın (varsayılan olarak Prefork etkin olabilir):
   ```bash
   sudo a2dismod mpm_prefork
   ```

2. Event MPM modülünü etkinleştirin:
   ```bash
   sudo a2enmod mpm_event
   ```

3. Apache’yi yeniden başlatın:
   ```bash
   sudo systemctl restart apache2
   ```

#### **Neden Önemli?**
- **Prefork MPM**, her bağlantı için bir işlem oluşturduğu için kaynak tüketimi yüksektir.
- **Event MPM**, asenkron olarak çalışır ve daha az kaynak kullanarak yüksek bağlantı sayısını yönetebilir.

---

### **2. Önbellekleme (Caching) Etkinleştirme**
Statik dosyaların hızlı bir şekilde sunulması için önbellekleme önemlidir.

#### **Adımlar:**
1. Apache önbellek modüllerini etkinleştirin:
   ```bash
   sudo a2enmod cache cache_disk
   ```

2. Önbellek yapılandırmasını Apache konfigürasyon dosyanıza ekleyin:
   ```apache
   <IfModule mod_cache.c>
       CacheQuickHandler On
       CacheLock On
       CacheRoot "/var/cache/apache2"
       CacheEnable disk /
       CacheIgnoreNoLastMod On
   </IfModule>
   ```

#### **Neden Önemli?**
- Önbellekleme, sık erişilen içeriklerin bellekte veya disk üzerinde tutulmasını sağlar, böylece sunucunun her istekte dosyayı yeniden oluşturması gerekmez.

---

### **3. HTTP/2 Kullanımı**
HTTP/2, bağlantıları çoklama (multiplexing) ile gecikmeyi azaltır ve daha hızlı veri aktarımı sağlar.

#### **Adımlar:**
1. HTTP/2 modülünü etkinleştirin:
   ```bash
   sudo a2enmod http2
   ```

2. SSL ile çalışan sanal sunucularınızın yapılandırmasına şu satırı ekleyin:
   ```apache
   <VirtualHost *:443>
       Protocols h2 http/1.1
       ...
   </VirtualHost>
   ```

#### **Neden Önemli?**
- HTTP/2, özellikle birden fazla dosyanın (CSS, JS, resimler) aynı anda indirildiği durumlarda büyük performans kazançları sağlar.

---

### **4. Statik Dosyaları Etkin Sunma**
Statik dosyalar sunulurken gereksiz işlemcileri (örneğin PHP) devreye sokmamak, performansı artırır.

#### **Adımlar:**
1. `mod_expires` ve `mod_headers` modüllerini etkinleştirin:
   ```bash
   sudo a2enmod expires headers
   ```

2. Statik dosyalar için önbellekleme başlıklarını yapılandırın:
   ```apache
   <FilesMatch "\.(jpg|jpeg|png|gif|css|js|ico|svg|woff|woff2|ttf|eot|otf|html|pdf)$">
       Header set Cache-Control "max-age=31536000, public"
   </FilesMatch>
   ```

#### **Neden Önemli?**
- Tarayıcı önbelleğini etkinleştirerek statik dosyaların tekrar indirilmeye çalışılmasını önlersiniz, bu da yükleme süresini azaltır.

---

### **5. Sıkıştırma (Compression) Etkinleştirme**
Dosyaları istemciye göndermeden önce sıkıştırmak, bant genişliğini azaltır ve aktarım hızını artırır.

#### **Adımlar:**
1. `mod_deflate` modülünü etkinleştirin:
   ```bash
   sudo a2enmod deflate
   ```

2. Sıkıştırma kurallarını yapılandırın:
   ```apache
   <IfModule mod_deflate.c>
       AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css text/javascript application/javascript application/json application/xml
   </IfModule>
   ```

#### **Neden Önemli?**
- Sıkıştırma, özellikle büyük boyutlu HTML, CSS ve JavaScript dosyalarının hızlı yüklenmesini sağlar.

---

### **6. KeepAlive Ayarlarını Optimize Etme**
KeepAlive, istemci-sunucu arasındaki bağlantıyı açık tutarak yeni bağlantıların oluşturulması için harcanan süreyi azaltır.

#### **Yapılandırma:**
Apache konfigürasyon dosyasına aşağıdaki ayarları ekleyin:
```apache
KeepAlive On
MaxKeepAliveRequests 100
KeepAliveTimeout 2
```

#### **Neden Önemli?**
- KeepAlive, istemci ile birden fazla dosya indirilirken aynı bağlantıyı kullanarak performansı artırır.

---

### **7. Reverse Proxy Kullanımı**
Dinamik istekleri veya API işlemlerini bir backend sunucuya (örneğin, Node.js) yönlendirmek için Apache reverse proxy olarak kullanılabilir.

#### **Adımlar:**
1. Gerekli modülleri etkinleştirin:
   ```bash
   sudo a2enmod proxy proxy_http
   ```

2. Sanal sunucu yapılandırmanıza şu satırları ekleyin:
   ```apache
   <VirtualHost *:80>
       ProxyPreserveHost On
       ProxyPass /api http://localhost:3000/
       ProxyPassReverse /api http://localhost:3000/
   </VirtualHost>
   ```

#### **Neden Önemli?**
- Apache, reverse proxy olarak çalışarak istemcilerin backend sunucuya erişimlerini kolaylaştırır ve güvenlik sağlar.

---

### **8. CDN Kullanımı**
Statik içerikleri (CSS, JavaScript, resimler) bir **Content Delivery Network (CDN)** üzerinden sunarak sunucu yükünü azaltabilir ve gecikmeyi düşürebilirsiniz.

#### **Neden Önemli?**
- CDN, içerikleri kullanıcıya coğrafi olarak en yakın sunucudan sunarak daha hızlı yükleme süreleri sağlar.

---

### **9. Apache’nin Bellek Kullanımını Azaltma**
Kullanılmayan modülleri devre dışı bırakmak, Apache’nin bellek tüketimini azaltır.

#### **Adımlar:**
```bash
sudo a2dismod autoindex status cgi cgid
```

#### **Neden Önemli?**
- Gereksiz modülleri kapatmak, daha az kaynak kullanımı sağlar ve sunucunun daha verimli çalışmasına olanak tanır.

---

### **10. Performans İzleme**
Apache’nin performansını izlemek için şu araçları kullanabilirsiniz:
- **`mod_status`**: Gerçek zamanlı Apache durumu.
- **`htop`**: Sunucu işlem durumu.
- **`iostat`** ve **`sar`**: Disk ve ağ performans analizi.

#### **Adımlar:**
1. `mod_status` modülünü etkinleştirin:
   ```bash
   sudo a2enmod status
   ```
2. Durum bilgilerini tarayıcıdan görüntüleyin:
   ```plaintext
   http://your-server/status
   ```

