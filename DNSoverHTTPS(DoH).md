## DNS over HTTPS (DoH) на RouterOS  
Минимальная версия  для DoH - 6.47.1.  
### Список серверов DoH провайдеров и URL-адреса DoH серверов:  
OpenDNS - https://doh.opendns.com/dns-query  
Quad9  - https://dns.quad9.net/dns-query  
Cloudflare - https://cloudflare-dns.com/dns-query  
Google Public DNS - https://dns.google/dns-query  
### Сертификаты  
Для Google Public DNS  
[GlobalSign Root CA - R2](https://pki.goog/repo/certs/gsr2.pem)
SHA-256: 69:E2:D0:6C:30:F3:66:16:61:65:E9:1D:68:D1:CE:E5:CC:47:58:4A:80:22:7E:76:66:60:86:C0:10:72:41:EB  
Для остальных  
[DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)
SHA-256: 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61  

### Настройка DNS over HTTPS  
- выбираем провайдера DoH  
- в IP - DNS и внесем в поле Use DoH Server URL-адрес DoH-сервера  
- установим флаг Verify DoH Certificate  
- устанавливаем сертификат (скачиваем и импортируем в System - Certificates, после импорта проверяем Fingerprint)  
