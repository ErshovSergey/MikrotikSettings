## Mikrotik - понятные логи при выдаче адресы с dhcp сервера  
### При выдаче сервера по dhcp - выполнить скрипт (добавлять в /dhcp-server script)  
```
:if ($leaseBound = 1) do={
/ip dhcp lease
:log info ("This is "."$[get [find address=$leaseActIP] comment] "."$[get [find address=$leaseActIP] host-name]")
}
```
Теперь при выдаче адреса с dhcp сервера в логах будет строка вида  
``` 
This is "коммент устройства получившего IP и его имя"
``` 
