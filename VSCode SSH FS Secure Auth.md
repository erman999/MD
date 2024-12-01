### **VSCode SSH FS Secure Auth**

**SSH Anahtarını Oluştur**  
- PuTTY Key Generator (PuTTYgen)'i çalıştır.  
- RSA şifrelemesini seç.  
- Uzunluğu 4096 bit olarak değiştir.  
- Passphrase bölümünü boş bırak (kaydederken emin misiniz diye sorarsa **Yes**'i seç).  

- Private Key'i şu şekilde kaydet:  
  `C:\Users\UserName\Documents\SSH\id_rsa.ppk`  

- Public Key'i, PuTTYgen'deki kutucuktan mouse ile seçip kopyala:  
  `ssh-rsa AAA........== rsa-key-20241130`  
  Server'da `/home/username/.ssh/authorized_keys` dosyasına yapıştır.

---

**Private Key'i OpenSSH Formatına Çevir**  
- VSCode SSH FS paketi **OpenSSH** formatında çalışıyor.  
- Eğer PuTTY ekranını kapadıysan `.ppk` anahtarını yeniden yükle.  

- Menüden şu işlemleri yap:
  - Conversions > Export OpenSSH Key > Anahtarı `id_rsa` adıyla aynı klasöre kaydet.  

---

**VSCode SSH FS Konfigürasyonu**  
- VSCode'a gir ve `Ctrl+Shift+P` tuşlarına bas.  
- **Preferences: Open Settings (JSON)** seçeneğini açarak `settings.json` dosyasını düzenle.  

- Aşağıdaki kodu ekle ve kendine göre düzenle:  
  ```json
  "sshfs.configs": [
      {
          "name": "MyServer",
          "host": "example.com",
          "username": "username",
          "privateKeyPath": "C:\\Users\\UserName\\Documents\\SSH\\id_rsa",
          "remotePath": "/home/username",
          "password": false
      }
  ]
  ```

---

### **SSH Server Konfigürasyonu**

**Anahtar ile Login'i Aktif Et**  
- Server'da SSH anahtarı ile login olmaya izin ver.  
  SSH konfigürasyon dosyasını aç:  
  ```bash
  sudo nano /etc/ssh/sshd_config
  ```  

- Aşağıdaki satırın aktif olduğundan emin ol:  
  ```plaintext
  PubkeyAuthentication yes
  ```  
  Eğer bu satırın başında `#` varsa kaldır ve kaydet.  

- Şifre ile girişi devre dışı bırakmak istiyorsan şu değişikliği yap:  
  ```plaintext
  PasswordAuthentication no
  ```  

- `AuthorizedKeysFile` satırının şu şekilde olduğunu kontrol et:  
  ```plaintext
  AuthorizedKeysFile .ssh/authorized_keys
  ```  

- Değişikliklerden sonra SSH servisini yeniden başlat:  
  ```bash
  sudo systemctl restart ssh
  ```  

- Bağlantıyı şu şekilde test et:  
  ```bash
  ssh -i C:\Users\UserName\Documents\SSH\id_rsa username@example.com
  ```

---

**Debugging (Bağlantı Sorunları)**  
- Eğer bağlanamıyorsan, aşağıdaki komutla logları kontrol et:  
  ```bash
  sudo tail -f /var/log/auth.log
  ```  

- Eğer sorun izin kaynaklıysa şu adımları uygula:  
  ```bash
  # .ssh dizininin var olduğundan ve doğru kullanıcıya ait olduğundan emin ol
  sudo chown -R username:username /home/username/.ssh

  # .ssh dizini için izinleri ayarla
  sudo chmod 700 /home/username/.ssh

  # authorized_keys dosyasının doğru kullanıcıya ait olduğundan emin ol
  sudo chown username:username /home/username/.ssh/authorized_keys

  # authorized_keys dosyası için izinleri ayarla
  sudo chmod 600 /home/username/.ssh/authorized_keys
  ```  

- Yukarıdaki adımları uyguladıktan sonra SSH'ı yeniden başlat:  
  ```bash
  sudo systemctl restart ssh
  ```

---

### **`pam_env` Modülünü Devre Dışı Bırak**
- `pam_env` modülünden kaynaklanan "deprecated reading of user environment enabled" uyarısını çözmek için:  
  SSH PAM dosyasını düzenle:  
  ```bash
  sudo nano /etc/pam.d/sshd
  ```  

- Aşağıdaki satırı bul:  
  ```plaintext
  session required pam_env.so readenv=1
  ```  

- Bu satırı şu şekilde değiştir ya da devre dışı bırak:  
  ```plaintext
  # session required pam_env.so readenv=1
  ```  
  veya:  
  ```plaintext
  session required pam_env.so
  ```  

- Değişikliklerden sonra SSH servisini yeniden başlat:  
  ```bash
  sudo systemctl restart ssh
  ```

---

### **Ekstra Güvenlik İpuçları**
- **Root Login'i Devre Dışı Bırak**:  
  Root kullanıcısıyla giriş yapmıyorsan, root login'i kapat:  
  ```plaintext
  PermitRootLogin no
  ```  

- **Fail2Ban Kur ve Aktif Et**:  
  Brute-force saldırılardan korunmak için:  
  ```bash
  sudo apt install fail2ban
  sudo systemctl enable --now fail2ban
  ```  
