# SendMailAfterReboot - отправка email после перезагрузки  
## Скрипт  SendMailAfterReboot
```
###################################################
# The ntp package is required for the correct functionality
# permission - read, write, sensitive, policy, password, sniff, test, ftp
# email account from
:local Eaccount "<emailacc>@gmail.com";
# email account to
:local toEaccount "<emailaccTO>@gmail.com";
# password emailaccTO>@gmail.com
:local pass "passwordemailacc";
:local smtpserv [:resolve "smtp.gmail.com"];
###################################################

/log info "Start script after reboot:"
:while ( [/system ntp client get status]!="synchronized" ) do={ :delay 10s }
:delay 10s
/log info "  - time updated; uptime: $[/system resource get uptime]"
:local es "$[/system identity get name] rebooted on $[/system clock get date] $[/system clock get time] uptime $[/system resource get uptime]"
:delay 90s
:local eb "Log contents (with 90 seconds delay):\r\n"
:foreach le in=[/log print as-value] do={
  :set eb ($eb.[:tostr [($le->"time")]]." ".[:tostr [($le->"topics")]].": ".[:tostr [($le->"message")]]."\r\n")
}
/tool e-mail send from="<$Eaccount>" to=$toEaccount server=$smtpserv \
port=587 user=$Eaccount password=$pass start-tls=yes \
subject=("$es") \
body=("$eb"."\n\n Logs \n\n"."$logMessages ");

/log info "  - send mail."
```
## Правило для запуска
```
/system scheduler
add name=SendMailAfterReboot on-event=":delay 15\r\
    \n/system script run SendMailAfterReboot" policy=ftp,reboot,read,write,policy,test,password,sniff,sensitive,romon start-time=startup
```
