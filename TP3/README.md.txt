## I. Setup réseau initial

3. Marche à suivre

B. On ajoute les routes vers les LAN

🌞 ping d'un client du  LAN1 vers un client du LAN 2

PC1> ping 10.3.2.1

84 bytes from 10.3.2.1 icmp_seq=1 ttl=62 time=39.849 ms
84 bytes from 10.3.2.1 icmp_seq=2 ttl=62 time=28.065 ms
84 bytes from 10.3.2.1 icmp_seq=3 ttl=62 time=24.839 ms
84 bytes from 10.3.2.1 icmp_seq=4 ttl=62 time=36.245 ms
84 bytes from 10.3.2.1 icmp_seq=5 ttl=62 time=35.925 ms

🌞 Afficher les adresses MAC des routeurs


R1#show arp

Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.3.12.1               -   c401.050e.0010  ARPA   FastEthernet1/0   <==
Internet  10.3.12.2              21   c402.052c.0000  ARPA   FastEthernet1/0   <==
Internet  10.3.1.1                6   0050.7966.6800  ARPA   FastEthernet0/1
Internet  10.3.1.2               11   0050.7966.6801  ARPA   FastEthernet0/1
Internet  192.168.122.1           1   5254.0023.04c2  ARPA   FastEthernet0/0
Internet  192.168.122.250         -   c401.050e.0000  ARPA   FastEthernet0/0
Internet  10.3.1.254              -   c401.050e.0001  ARPA   FastEthernet0/1


C. Accès internet
➜ Déjà accès internet ?
🌞 Prouvez que vous avez déjà un accès internet sur r1

R1#ping 8.8.8.8

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 60/61/64 ms

🌞 Accès internet LAN1

normalement, le LAN1 étant directement branché à r1, y'a déjà internet
prouvez, depuis un client du LAN1 qu'il a un accès internet

PC1> ping 8.8.8.8

84 bytes from 8.8.8.8 icmp_seq=1 ttl=112 time=33.074 ms
84 bytes from 8.8.8.8 icmp_seq=2 ttl=112 time=42.382 ms
84 bytes from 8.8.8.8 icmp_seq=3 ttl=112 time=31.457 ms
84 bytes from 8.8.8.8 icmp_seq=4 ttl=112 time=23.126 ms
84 bytes from 8.8.8.8 icmp_seq=5 ttl=112 time=27.420 ms


🌞 Accès internet LAN2

prouvez, depuis un client du LAN2 qu'il a un accès internet

PC3> ping 8.8.8.8

84 bytes from 8.8.8.8 icmp_seq=1 ttl=111 time=40.692 ms
84 bytes from 8.8.8.8 icmp_seq=2 ttl=111 time=40.399 ms
84 bytes from 8.8.8.8 icmp_seq=3 ttl=111 time=34.989 ms
84 bytes from 8.8.8.8 icmp_seq=4 ttl=111 time=36.044 ms
84 bytes from 8.8.8.8 icmp_seq=5 ttl=111 time=35.798 ms

## Partie II. Router-on-a-stick

3. Marche à suivre
A. VLANs

Mise en place de VLAN:

IOU1#conf t
IOU1(config)#vlan 10
IOU1(config-vlan)#name admin
IOU1(config-vlan)#exit

IOU1(config)#interface Ethernet0/1
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 10
IOU1(config-if)#exit

Trunk:

IOU1(config)#interface Ethernet0/2
IOU1(config-if)#switchport trunk encapsulation dot1q
IOU1(config-if)#switchport mode trunk
IOU1(config-if)#switch
IOU1(config-if)#switchport trunk allowed vlan add 10,20,30
IOU1(config-if)#exit

🌞 Tests de ping

une fois tous les VLANs en place, les membres d'un même VLAN sont autorisés à se ping

prouvez que client1 et client3 peuvent se ping

PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=1.704 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=1.478 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=2.689 ms
84 bytes from 10.3.1.2 icmp_seq=4 ttl=64 time=2.268 ms
84 bytes from 10.3.1.2 icmp_seq=5 ttl=64 time=2.506 ms


B. Routeur
Configuration du routeur:

R1(config)#interface fastEthernet1/0.10
R1(config-subif)#encapsulation dot1Q 10
R1(config-subif)#ip address 10.3.1.254 255.255.255.0
R1(config)#interface fastEthernet 1/0
R1(config-subif)#exit
🌞 Tests de ping
le routeur peut ping tout le monde:

R1#ping 10.3.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/44/64 ms

R1#ping 10.3.2.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.2.1, timeout is 2 seconds:
!!!!!

le LAN1 et le LAN2 se ping:


PC2> show ip

NAME        : PC2[1]
IP/MASK     : 10.3.2.1/24
GATEWAY     : 10.3.2.254
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 20025
RHOST:PORT  : 127.0.0.1:20026
MTU         : 1500


PC2> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=63 time=30.096 ms
84 bytes from 10.3.1.1 icmp_seq=2 ttl=63 time=16.021 ms
84 bytes from 10.3.1.1 icmp_seq=3 ttl=63 time=13.302 ms
84 bytes from 10.3.1.1 icmp_seq=4 ttl=63 time=20.684 ms
84 bytes from 10.3.1.1 icmp_seq=5 ttl=63 time=14.254 ms

🌞 Tests de ping

le routeur peut ping internet

R1#ping 8.8.8.8

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 20/65/88 ms
R1#


n'importe quel client a un accès internet


PC1> ping 8.8.8.8

84 bytes from 8.8.8.8 icmp_seq=1 ttl=112 time=33.769 ms
84 bytes from 8.8.8.8 icmp_seq=2 ttl=112 time=33.275 ms
84 bytes from 8.8.8.8 icmp_seq=3 ttl=112 time=30.643 ms


