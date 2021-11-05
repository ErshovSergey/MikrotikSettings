### Настройка Mikrotik как клиента WireGuard  



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
[по мотивам](https://forum.mikrotik.com/viewtopic.php?t=169011#p829164)  
```
/routing table add fib name=via-wg
/routing rule add action=lookup disabled=no src-address=172.16.1.0/24 table=via-wg
/ip firewall nat add action=masquerade chain=srcnat out-interface=wg0
/ip route add dst-address=0.0.0.0/0 gateway=10.66.66.1 routing-table=via-wg
```
