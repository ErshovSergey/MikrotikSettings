# MikrotikSettings - настройки RouterOS Mikrotik, чтобы не искать
- [DNS over HTTPS (DoH) на RouterOS](./DNSoverHTTPS(DoH).md)  
- [Настройка mtu и l2mtu если провайдер Билайн](./beelinel2tp_mtu_l2mtu.md)  
- [speedtest с Mikrotik](./speedtest-with-mikrotik.md)  
- [firewall on RouterOS](./firewall-on-routeros.md)  
- [Mikrotik CHR - обновление](./MikrotikCHR-update.md)  
- [SendMailAfterReboot - отправка email после перезагрузки](./SendMailAfterReboot.md)  
- [Настройка Mikrotik как клиента WireGuard](./WireGuard_on_RouterOS7.md)
### Проблема - сайты загружаются выборочно и неполностью  
Размер MTU при подключении l2tp, IPoE не обеспечивает правильной загрузки  
Решение  
- изменить максимальный размер сегмента. Т.е. идем в IP-->Firewall-->Mangle и создаем правила:  
Chain: Forward, Protocol: (6)TCP, In-Interface:"ethernet1 (туда провод от провайдера приходит)", Action:Change MSS, New-MSS: ставим меньше чем MTU на 40.  
Т.е. если у Вас MTU 1492, то New-MSS будет 1452  

