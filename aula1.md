Broadcast ip: 172.16.10.0

PC 3 (MAC 00:21:5a:61:2d:ef)
```
ifconfig eth0 172.16.10.1/24
route -n
ping 172.16.10.254
```

PC 4 (MAC 00:21:5a:61:2f:24)
```
ifconfig eth0 172.16.10.254/24
route -n
ping 172.16.10.1
```

PC 2 (MAC 00:21:5a:61:2e:c3)
```
ifconfig eth0 172.16.11.1/24
route -n
```

No PC 4, entrar no gtkterminal e executar
```
telnet 172.16.1.10
```
password: 8nortel

Depois, criar vlan 0
```
configure terminal
vlan 10
end
show vlan id 10
```

Como o PC 4 está ligado ao switch, configuramos as portas:
```
configure terminal
interface fastethernet 0/15
switchport mode access
switchport access vlan 10
end
show running-config interface fastethernet 0/15
show interfaces fastethernet 0/15 switchport
```

Fazer o mesmo para a porta 13:
```
configure terminal
interface fastethernet 0/13
switchport mode access
switchport access vlan 10
end
show running-config interface fastethernet 0/13
show interfaces fastethernet 0/13 switchport
```
Criamos vlan10, com PC3 ligado à port 13, e com o PC15 ligado à port 15.


Agora criar vlan 1 e adicionar a porta 9 (ligada ao PC 2)
```
configure terminal
vlan 11
end
show vlan id 11

configure terminal
interface fastethernet 0/9
switchport mode access
switchport access vlan 11
end
show running-config interface fastethernet 0/9
show interfaces fastethernet 0/9 switchport
```
Em todos os PCs
```
echo 0 > /proc/sys/net/ipv4/icmp_echo_ignore_broadcasts
```
pings...

Experiencia 3

Ligar cabos do E2 do 4 ao switch;
Adicionamos port em que E2 do 4 está;
Criamos endereço do 4 em vlan 1

No PC4:
```
ifconfig eth1 172.16.11.253
ifconfig
echo 1 > /proc/sys/net/ipv4/ip_forward 
```
Last ifconfig is to check if was created
IPv4=172.16.11.253
MAC=00:c0:df:04:20:99

No PC 3:
```
 route add -net 172.16.11.0/24 gw 172.16.10.254
 route -n
```

No PC 2:
```
 route add -net 172.16.10.0/24 gw 172.16.11.253
 route -n
```

No I thinks this really might work
You may now do your crazy stuff




