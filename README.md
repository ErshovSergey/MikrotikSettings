# MikrotikSettings - настройки RouterOS Mikrotik, чтобы не искать
### Проблема - сайты загружаются выборочно и неполностью  
Размер MTU при подключении l2tp, IPoE не обеспечивает правильной загрузки  
Решение  
- изменить максимальный размер сегмента. Т.е. идем в IP-->Firewall-->Mangle и создаем правила:  
Chain: Forward, Protocol: (6)TCP, In-Interface:"ethernet1 (туда провод от провайдера приходит)", Action:Change MSS, New-MSS: ставим меньше чем MTU на 40.  
Т.е. если у Вас MTU 1492, то New-MSS будет 1452  



    Active topics Forum 

Search…

MikroTik App

    Home
    Forum index
    RouterOS
    General

### Public-Mikrotik-Bandwidth-Test-Server(s)  
Источник (https://forum.mikrotik.com/viewtopic.php?f=2&t=104266)  
IPv4: **23.162.144.120**  
btest username: **btest**  
btest password: **btest**  

