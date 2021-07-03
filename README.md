# MikrotikSettings - настройки RouterOS Mikrotik, чтобы не искать
### Проблема - сайты загружаются выборочно и неполностью  
Размер MTU при подключении l2tp, IPoE не обеспечивает правильной загрузки  
Решение  
- изменить максимальный размер сегмента. Т.е. идем в IP-->Firewall-->Mangle и создаем правила:  
Chain: Forward, Protocol: (6)TCP, In-Interface:"ethernet1 (туда провод от провайдера приходит)", Action:Change MSS, New-MSS: ставим меньше чем MTU на 40.  
Т.е. если у Вас MTU 1492, то New-MSS будет 1452  



### Public-Mikrotik-Bandwidth-Test-Server(s)  
Источник (https://forum.mikrotik.com/viewtopic.php?f=2&t=104266)  
IPv4: **23.162.144.120**  
btest username: **btest**  
btest password: **btest**  

### Правила firiwall  
Создаем списки интерфесов локальной сети (LAN_list) и внешней сети (WAN_list) - Interfaces, вкладка Interfaces list  
Добавляем в списки интерфейсы.

#### Минимальный набор правил для firewall
```
/ip firewall filter
add action=accept chain=input comment="accept established,related,untracked" connection-state=established,related,untracked
add action=drop chain=input comment="drop invalid" connection-state=invalid
add action=accept chain=input comment="accept ICMP" protocol=icmp
;;add action=accept chain=input comment="accept to local loopback (for CAPsMAN)" dst-address=127.0.0.1
add action=drop chain=input comment="drop all not coming from LAN" in-interface-list=!LAN_list
```
#### NAT
```
/ip firewall nat
add action=masquerade chain=srcnat comment="defconf: masquerade" ipsec-policy=out,none \
    out-interface-list=WAN_list
```
#### Черная дыра для "неправильных" адресов  
```
/ip route
add comment=" RFC1122 , RFC3330 \? RFC1700" distance=249 dst-address=0.0.0.0/8 type=blackhole
add comment="RFC 1918" distance=249 dst-address=10.0.0.0/8 type=blackhole
add comment="RFC1700, page 181" distance=249 dst-address=14.0.0.0/8 type=blackhole
add comment="RFC6598 " distance=249 dst-address=100.64.0.0/10 type=blackhole
add comment=https://wiki.mikrotik.com/wiki/BOGON_Address_List distance=249 dst-address=127.0.0.0/8 type=blackhole
add comment=" RFC3927" distance=249 dst-address=169.254.0.0/16 type=blackhole
add comment="RFC 1918" distance=249 dst-address=172.16.0.0/12 type=blackhole
add comment=RFC6890 distance=249 dst-address=192.0.0.0/24 type=blackhole
add comment="RFC5737 " distance=249 dst-address=192.0.2.0/24 type=blackhole
add comment=RFC3068 distance=249 dst-address=192.88.99.0/24 type=blackhole
add comment="RFC 1918" distance=249 dst-address=192.168.0.0/16 type=blackhole
add comment="RFC2544, RFC6815 " distance=249 dst-address=198.18.0.0/15 type=blackhole
add comment="RFC5737 " distance=249 dst-address=198.51.100.0/24 type=blackhole
add comment="RFC5737 " distance=249 dst-address=203.0.113.0/24 type=blackhole
add comment=" Class D, RFC5771 " distance=249 dst-address=224.0.0.0/4 type=blackhole
add comment=RFC1122 distance=249 dst-address=240.0.0.0/4 type=blackhole
```
#### Защита от сканирования
```
/ip firewall mangle add chain=input action=add-src-to-address-list tcp-flags=syn connection-state=new protocol=tcp psd=21,3s,3,1 address-list=PSD address-list-timeout=1d in-interface-list=WAN log=yes log-prefix="Port Scan Detection" comment="Port Scan Detection"
/ip firewall raw add chain=prerouting action=drop in-interface-list=WAN log=no log-prefix="" src-address-list=PSD comment="Port Scan Detection"
```

### CAPS  
#### CAPS channel
```
/caps-man channel
add band=2ghz-b/g/n control-channel-width=20mhz extension-channel=disabled frequency=2462,2437,2412 name=channel-2.4 reselect-interval=1h save-selected=yes
add band=5ghz-a/n/ac extension-channel=Ceee frequency=5180,5220,5745,5785 name=channel-5 reselect-interval=1d save-selected=yes
```
#### CAPS security
```
/caps-man security
add authentication-types=wpa2-psk encryption=aes-ccm group-key-update=1h name=security passphrase=PssworD
add authentication-types=wpa2-psk encryption=aes-ccm group-key-update=1h name=security5 passphrase=PssworD
```
