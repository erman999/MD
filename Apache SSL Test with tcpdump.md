## **Ağ ve SSL Kontrolleri**

Bu döküman, bir sistemde ağ bağlantılarını tespit etme, SSL güvenliğini kontrol etme ve SSL kullanımıyla ilgili durumları analiz etme süreçlerini detaylı bir şekilde anlatmaktadır. Her bir adımın amacı ve kullanılan komutlar açıklanmıştır.

---

### **1. Network Tespiti**

Bir sistemde hangi ağ bağlantılarının mevcut olduğunu tespit etmek için `ip link show` komutu kullanılır. Bu komut, ağ arayüzlerinin durumunu ve yapılandırmalarını gösterir.

#### **Komut:**
```bash
ip link show
```

#### **Açıklama:**
- `lo`: **Loopback arayüzü** olarak bilinir. Sistem içindeki (iç ağdaki) haberleşmeler için kullanılır.
- `enx` veya `eth`: **Harici ağ arayüzleri**. Genellikle internete veya dış ağlara erişimi ifade eder. Örneğin:
  - `enx...`: Ethernet adaptörleri.
  - `wlan...`: Kablosuz ağ adaptörleri.

#### **Örnek Çıktı:**
```plaintext
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enx5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 12:34:56:78:9a:bc brd ff:ff:ff:ff:ff:ff
```

- `lo`: Sistem içi haberleşmeler (örneğin, `127.0.0.1` loopback adresi).
- `enx5`: Harici ağa bağlanan adaptör (örneğin, bir Ethernet portu).

#### **Amaç:**
Bu adımda, hangi arayüzlerin sistemin iç ve dış iletişimlerini sağladığını belirliyoruz.

---

### **2. SSL Kontrolleri**

Sistemdeki SSL bağlantılarını analiz etmek ve şifrelenmiş verilerin akışını gözlemlemek için `tcpdump` aracı kullanılır. 

#### **Amaç:**
- Node.js sunucusunun Apache üzerinden SSL kullanarak güvenli şekilde çalışıp çalışmadığını kontrol etmek.
- SSL kullanımı olmadan yapılan bağlantılardaki güvenlik açıklarını tespit etmek.

---

#### **a. Proxy ile SSL Kullanımı Kontrolü**

Apache, gelen HTTPS (443 portu) bağlantılarını Node.js sunucusuna iletmek için bir proxy olarak çalışıyorsa bu yapı kontrol edilebilir.

##### **Komut:**
```bash
sudo tcpdump -i enx5 port 443 -A
```

##### **Açıklama:**
- `-i enx5`: Harici ağ arayüzünde (örneğin, `enx5`) veri akışını dinler.
- `port 443`: SSL trafiğini hedef alır (HTTPS için standart port).
- `-A`: Paketleri ASCII formatında görüntüler, böylece içerik okunabilir.

##### **Senaryo:**
- **Apache, HTTPS bağlantısını Node.js sunucusuna iletir**. 
- Apache dışarıya SSL sertifikası kullanarak 443 portundan cevap verir.
- Node.js sunucusu, Apache'den gelen veriyi genellikle **şifresiz (HTTP)** olarak alır (port 5000 gibi).

---

#### **b. Apache ve Node.js Arasında Şifrelenmemiş Trafik**

Node.js ve Apache arasında SSL kullanılmıyorsa, iç ağda HTTP trafiği analiz edilerek güvenlik durumu kontrol edilir.

##### **Komut:**
```bash
sudo tcpdump -i lo port 5000 -A
```

##### **Açıklama:**
- `-i lo`: Loopback arayüzünü (iç ağ) dinler. Apache ve Node.js arasındaki veri aktarımı bu arayüzden geçebilir.
- `port 5000`: Node.js sunucusunun dinlediği portu hedef alır.
- `-A`: Paket içeriklerini ASCII formatında gösterir.

##### **Sorun:**
Bu şekilde dinleme yapıldığında, Apache ve Node.js arasında SSL kullanılmadığı için:
- Veri düz metin (plain text) olarak gözükebilir.
- SSL paketlerinin eksikliği, sistemin güvenli olmadığını gösterir.

---

### **3. SSL’siz Kontroller**

Eğer Apache gibi bir proxy sunucusu kullanılmıyorsa ve Node.js sunucusu doğrudan bir port (örneğin, 8000) üzerinden çalışıyorsa, bağlantılar tamamen açık şekilde yapılabilir.

#### **Komut:**
```bash
sudo tcpdump -i enx5 port 8000 -A
```

#### **Açıklama:**
- `-i enx5`: Harici ağ adaptörünü dinler (örneğin, internete çıkan port).
- `port 8000`: Node.js sunucusunun dinlediği portu hedef alır.
- `-A`: ASCII formatında paket içeriklerini gösterir.

#### **Sonuç:**
Bu senaryoda:
- Sayfa içerikleri, **headers (başlıklar)** ve **data (veri)** düz metin olarak görülebilir.
- Şifrelenmemiş bağlantılar ciddi güvenlik açıklarına neden olur.

---

### **4. Güvenlik Önerileri**

#### **a. SSL Sertifikası Kullanımı**
- Apache, Let’s Encrypt gibi bir sertifika otoritesi ile SSL sertifikası almalı.
- Tüm dış bağlantılar 443 portundan, SSL ile şifrelenmiş şekilde yapılmalı.

#### **b. Apache ve Node.js Arasında SSL Kullanımı**
- Node.js sunucusunu da SSL destekleyecek şekilde yapılandırın.
- Alternatif olarak, Apache ve Node.js arasındaki trafiği iç ağdaki başka bir proxy ile şifreleyin.

#### **c. HTTP’den HTTPS’ye Yönlendirme**
- Apache'de HTTP trafiğini tamamen devre dışı bırakmak yerine HTTPS'ye yönlendirme ekleyin:
  ```apache
  RewriteEngine On
  RewriteCond %{HTTPS} off
  RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
  ```

#### **d. Ekstra Güvenlik**
- `tcpdump` gibi araçlarla düzenli kontrol yaparak sistemde SSL kullanımıyla ilgili eksiklikleri belirleyin.
- Node.js uygulamasında şifreli bağlantılar için yalnızca `https.createServer` kullanmayı düşünün.

---
