### Adım 1: Node.js Uygulamasını Güncelleyin

1. `socket.io` paketini yükleyin:
   ```bash
   npm install socket.io
   ```

2. `app.js` dosyanızı, `Socket.IO` kullanarak bir WebSocket sunucusu ekleyecek şekilde düzenleyin:
   ```javascript
   const express = require('express');
   const http = require('http');
   const { Server } = require('socket.io');

   const app = express();
   const server = http.createServer(app);
   const io = new Server(server);

   const PORT = 3000; // Node.js sunucusu için port

   // Basit bir API route tanımlayın
   app.get('/api/test', (req, res) => {
       res.json({ message: 'Merhaba, Node.js API!' });
   });

   // WebSocket bağlantılarını yönetin
   io.on('connection', (socket) => {
       console.log('Bir kullanıcı bağlandı.');

       // Örnek bir etkinlik işleyici
       socket.on('message', (msg) => {
           console.log(`Mesaj alındı: ${msg}`);
           socket.emit('reply', `Yanıt: ${msg}`);
       });

       // Bağlantı kesilmesi durumunda
       socket.on('disconnect', () => {
           console.log('Bir kullanıcı bağlantısını kesti.');
       });
   });

   // Sunucuyu başlatın
   server.listen(PORT, '127.0.0.1', () => {
       console.log(`Node.js sunucusu http://localhost:${PORT} adresinde çalışıyor.`);
   });
   ```

Bu yapılandırmada:
- `/api/test` route’u bir JSON nesnesi döndürür.
- `Socket.IO`, aynı sunucuda WebSocket bağlantılarını dinler.

---

### Adım 2: Apache Ayarlarını Güncelleyin

1. Apache konfigürasyon dosyanızı açın:
   ```bash
   sudo nano /etc/apache2/sites-available/your-site-le-ssl.conf
   ```

2. WebSocket bağlantılarını yönlendirmek için `<VirtualHost *:443>` bloğuna şu proxy ayarlarını ekleyin:
   ```apache
   # API ve WebSocket Proxy ayarları
   ProxyPass /api http://localhost:3000/api
   ProxyPassReverse /api http://localhost:3000/api

   # WebSocket Proxy ayarları
   ProxyPass /socket.io http://localhost:3000/socket.io
   ProxyPassReverse /socket.io http://localhost:3000/socket.io

   # WebSocket bağlantı yükseltme başlıkları
   RewriteEngine on
   RewriteCond %{HTTP:Upgrade} websocket [NC]
   RewriteCond %{HTTP:Connection} upgrade [NC]
   RewriteRule /socket.io/(.*) ws://localhost:3000/socket.io/$1 [P,L]
   ```

3. Dosyayı kaydedin ve kapatın.

---

### Adım 3: Apache'yi Yeniden Yükleyin

1. Yapılandırma hatalarını kontrol edin:
   ```bash
   sudo apache2ctl configtest
   ```

2. Değişiklikleri uygulamak için Apache'yi yeniden yükleyin:
   ```bash
   sudo systemctl reload apache2
   ```

---

### Adım 4: WebSocket Sunucusunu Test Edin

1. Basit bir WebSocket istemcisi kullanarak bağlantıyı test edin (örneğin, bir tarayıcı konsolu veya HTML sayfası).

   Örnek istemci kodu:
   ```html
   <script src="https://cdn.socket.io/4.0.0/socket.io.min.js"></script>
   <script>
       const socket = io('https://your-site.com');

       socket.on('connect', () => {
           console.log('WebSocket sunucusuna bağlanıldı.');

           // Bir mesaj gönderin
           socket.emit('message', 'Merhaba, sunucu!');

           // Yanıtları dinleyin
           socket.on('reply', (msg) => {
               console.log('Sunucudan yanıt:', msg);
           });
       });

       socket.on('disconnect', () => {
           console.log('WebSocket sunucusundan bağlantı kesildi.');
       });
   </script>
   ```

2. HTML dosyasını tarayıcınızda açın ve konsolda bağlantı loglarını kontrol edin.

---

### Adım 5: Güvenlik ve İzleme

- **Port 3000'i yalnızca localhost’a sınırlandırın**: WebSocket sunucusunun harici erişime açık olmadığından emin olun.
- **`screen` ile izleme yapın**: Uygulamanızı bir `screen` oturumunda çalıştırarak yönetebilirsiniz:
   ```bash
   screen -S node-api
   node app.js
   ```
   Oturumu `Ctrl+A D` ile ayırabilirsiniz.

- **Oturumu yeniden bağlamak için**:
   ```bash
   screen -r node-api
   ```

---

Bu yapılandırma ile hem API’niz hem de WebSocket sunucunuz aynı Node.js uygulaması üzerinde çalışacak ve Apache2 tarafından güvenli bir şekilde proxy’lenecektir. İstemciler artık `https://your-site.com` üzerinden hem REST API’sini hem de WebSocket özelliklerini kullanabilir.
