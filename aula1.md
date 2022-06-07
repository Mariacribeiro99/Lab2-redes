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
(I dont think this part is needed)
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
Criamos vlan10, com PC3 ligado à port 13, e com o PC4 ligado à port 15.


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
ifconfig eht1 up
ifconfig eth1 172.16.11.253/24
ifconfig
echo 1 > /proc/sys/net/ipv4/ip_forward 
echo 0 > /proc/sys/net/ipv4/icmp_echo_ignore_broadcasts
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
Configurar a porta 11 no switch para vlan11:
```
configure terminal
interface fastethernet 0/11
switchport mode access
switchport access vlan 11
end
show running-config interface fastethernet 0/11
show interfaces fastethernet 0/11 switchport
```

PC4:
eth0-->IP:172.16.10.254 MAC:00:21:5a:61:2f:24
eth1-->IP:172.16.11.253 MAC:00:c0:df:04:20:99



EXPERIÊNCIA 4
Configurar router comercial para rede interna(.254) e externa(.19), sem NAT

- Ligar router à rede interna
```
configure terminal
interface gigabitethernet 0/0     
ip address 172.16.11.254 255.255.255.0 
no shutdown 
*ip nat inside* (I dont think this command is needed)        
exit
show interface gigabitethernet 0/0
```

- Ligar Router à rede externa
```
configure terminal
interface gigabitethernet 0/1
ip address 172.16.1.19 255.255.255.0 
no shutdown 
*ip nat outside*(dont this this is needed)
exit

```
_______________________________________________________
_______________________________________________________
»configuar portas, ligar os GE do Router às VLANs

GigabitEthernet 0/0 à porta 17:
```
configure terminal
interface fastethernet 0/17
switchport mode access
switchport access vlan 11
end
show running-config interface fastethernet 0/17
show interfaces fastethernet 0/17 switchport
```
GigabitEthernet 0/1: Ligar à Netlab, ou seja 1.1

________________________________________________________
________________________________________________________

»no tux3: (tornar o tux4 o router default do tux3)
```
route add default gw 172.16.10.254
```
»no tux4 (tornar o Rc o router default do tux4):
```
route add default gw 172.16.11.254
```
»no tux2 (tornar o Rc o router default do tux2):
```
route add default gw 172.16.11.254
```

________________________________________________________
»in tux2
```
echo 0 > /proc/sys/net/ipv4/conf/eth0/accept_redirects
echo 0 > /proc/sys/net/ipv4/conf/all/accept_redirects
```

»Caso tenha criado antes, apagar agora
(Dont think this is needed but lets do this so we dont have any interferences)
```
route del -net 172.16.10.0/24 gw 172.16.11.253
```
»in tux3
```
route add -net 172.16.11.0/24 gw 172.16.10.254
```
________________________________________________________
________________________________________________________

»in router console (gtkterm)
Adicionamos comunicaçao entre PC3 e router
```
conf t
ip route 172.16.10.0 255.255.255 172.16.11.253
end
```

/* neste ponto temos que conseguir pingar tudo de todos os pc */


Configurar NAT
>>No Router
```
conf t
interface gigabitethernet 0/0
ip nat inside 
exit

interface gigabitethernet 0/1
ip nat outside 
exit
```
>>Still no router
```
ip nat pool ovrld 172.16.1.19 172.16.1.19 prefix 24       
ip nat inside source list 1 pool ovrld overload       
access-list 1 permit 172.16.10.0 0.0.0.7       
access-list 1 permit 172.16.11.0 0.0.0.7      
ip route 0.0.0.0 0.0.0.0 172.16.1.254 
ip route 172.16.10.0 255.255.255.0 172.16.11.253 
end

```
Voila, we have Internet

EXPERIENCIA 5
Configuração DNS
```

echo $'search netlab.fe.up.pt\nnameserver 172.16.1.2' > /etc/resolv.conf


