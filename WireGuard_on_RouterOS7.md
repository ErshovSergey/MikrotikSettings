### Настройка Mikrotik как клиента WireGuard  
src:
1. https://habr.com/ru/post/577328/

* * *
1. Отключить IPv6:
- Создать файл */etc/sysctl.d/10-ipv6-privacy.conf*
```
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
```
- Применить настройки  
```
sysctl -p /etc/sysctl.d/10-ipv6-privacy.conf
```
2. Установить WireGuard скриптом https://github.com/angristan/wireguard-install, создать пира с именем mikrotik, всё остальное по умолчанию.
3. В конфиге WireGuard `nano /etc/wireguard/wg0.conf` :
- убрать упоминания IPv6 адресов (Interface - Address, Peer - AllowedIPs) ;
5. Перезагрузить сервер.
6. Проверить статус WireGuard `systemctl status wg-quick@wg0` , должен быть active.
7. Создать на Mikrotik интерфейс WireGuard:
 `interface/wireguard/add name=wg0`
8. Создать на Mikrotik пира:
`/interface/wireguard/peers add allowed-address=0.0.0.0/0 interf
ace=wg0 persistent-keepalive=20 public-key="PUBLIC-KEY" endpoint-address=ENDPOINT_ADDRESS endpoint-port=ENDPOINT_PORT preshared-key="PRESHARED_KEY"`
Значение `PUBLIC-KEY` заменить на `public key` из вывода команды `wg show wg0` на сервере; значения `ENDPOINT_ADDRESS, ENDPOINT_PORT, PRESHARED_KEY` заменить на значения из конфига WireGuard.
9. На сервере в конфиге WireGuard `nano /etc/wireguard/wg0.conf` :
- заменить PublicKey на значение public-key из вывода команды `interface/wireguard/print` на Mikrotik.
10. Перезапустить Wireguard `systemctl restart wg-quick@wg0`, проверить статус WireGuard `systemctl status wg-quick@wg0` , должен быть active.
11. Добавить ip-адрес на Mikrotik
IP/Addresses/+
	Address - 10.66.66.2 (должен совпадать с `allowed ips`)
	Interface - wg0.
12. Проверка - ping с сервера 10.66.66.2 (Mikrotik); ping с Mikrotik 10.66.66.1 (сервер).


#### Настройка Mikrotik для работы подключенных к Mikrotik хостов через тунель WireGuard  
##### Настройка dhcp для поключенных хостов  
Адрес интерфейса WireGuard  
```/ip address add address=172.16.1.1/24 interface=bridge1 network=172.16.1.0```   
Пул адресов для хостов  
```/ip pool add name=pool1 ranges=172.16.1.10-172.16.1.50```   
DCHP сервер  
```
/ip dhcp-server add address-pool=pool1 interface=bridge1 name=server1
/ip dhcp-server network add address=172.16.1.0/24 dns-server=8.8.8.8 gateway=172.16.1.1
```

##### Настройка роутинга подключенных хостов через WireGuard  
```
/ip firewall mangle
add action=change-mss chain=forward new-mss=1420 out-interface=wg0 passthrough=yes protocol=tcp tcp-flags=syn tcp-mss=1421-65535
add action=change-mss chain=forward in-interface=wg0 new-mss=1420 passthrough=yes protocol=tcp tcp-flags=syn tcp-mss=1421-65535
```

[по мотивам](https://forum.mikrotik.com/viewtopic.php?t=169011#p829164)  
```
/routing table add fib name=via-wg
/routing rule add action=lookup disabled=no src-address=172.16.1.0/24 table=via-wg
/ip firewall nat add action=masquerade chain=srcnat out-interface=wg0
/ip route add dst-address=0.0.0.0/0 gateway=10.66.66.1 routing-table=via-wg
```
