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

No PC 3, entrar no gtkterminal e executar
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

Como o PC 3 está ligado à porta 5 do switch:
```
configure terminal
interface fastethernet 0/5
switchport mode access
switchport access vlan 10
end
show running-config interface fastethernet 0/5
show interfaces fastethernet 0/5 switchport
```

Fazer o mesmo para a porta 9, que está ligada ao PC 4.
```
configure terminal
interface fastethernet 0/9
switchport mode access
switchport access vlan 10
end
show running-config interface fastethernet 0/9
show interfaces fastethernet 0/9 switchport
```

Agora criar vlan 1 e adicionar a porta 2 (ligada ao PC 2)
```
configure terminal
vlan 11
end
show vlan id 11

configure terminal
interface fastethernet 0/2
switchport mode access
switchport access vlan 11
end
show running-config interface fastethernet 0/2
show interfaces fastethernet 0/2 switchport
```


pings...
