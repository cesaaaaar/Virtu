NAME=LAN
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.2.1.254
NETMASK=255.255.255.0


sudo nmcli con reload
sudo nmcli con up enp0s3


[rocky@routeur ~]$ sudo vi /etc/sysconfig/network-scripts/ifcfg-enp0s8


[rocky@routeur ~]$ sudo nmcli con reload


[rocky@routeur ~]$ sudo nmcli con up enp0s8
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/5)


[rocky@routeur ~]$ ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=58 time=277 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=58 time=22.8 ms
64 bytes from 1.1.1.1: icmp_seq=3 ttl=58 time=26.6 ms
64 bytes from 1.1.1.1: icmp_seq=4 ttl=58 time=26.4 ms


4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 22.808/88.307/277.431/109.201 ms

[rocky@routeur ~]$ ip a
1: lo: <LOOPBACK, UP, LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
inet 127.0.0.1/8 scope host lo
valid_lft forever preferred_lft forever
inet6 :: 1/128 scope host
valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST, MULTICAST, UP, LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
link/ether 08:00:27:5b:7f :d6 brd ff :ff :ff :ff:ff:ff
inet 10.2.1.254/24 brd 10.2.1.255 scope global nopref ixroute enp0s3
valid_lft forever preferred_lft forever
inet6 fe80 :: a00:27ff :fe5b:7fd6/64 scope link
valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST, MULTICAST, UP, LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
link/ether 08:00:27:64:58:fb brd ff:ff:ff:ff:ff:ff
inet 192.168.122.73/24 brd 192.168.122.255 scope global dynamic nopref ixroute enp0s8
valid_lft 3362sec preferred_lft 3362sec
inet6 fe80 :: a00:27ff :fe64:58fb/64 scope link
valid lft forever preferred lft forever


PC1> ip 10.2.1.1
Checking for duplicate address...
PC1 : 10.2.1.1 255.255.255.0

PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.2.1.1/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20003
RHOST:PORT  : 127.0.0.1:20004
MTU         : 1500

PC1> ping 10.2.1.254

84 bytes from 10.2.1.254 icmp_seq=1 ttl=64 time=1.005 ms
84 bytes from 10.2.1.254 icmp_seq=2 ttl=64 time=2.259 ms
84 bytes from 10.2.1.254 icmp_seq=3 ttl=64 time=5.050 ms
84 bytes from 10.2.1.254 icmp_seq=4 ttl=64 time=5.451 ms
84 bytes from 10.2.1.254 icmp_seq=5 ttl=64 time=4.143 ms

PC1> ip 10.2.1.1 10.2.1.254
Checking for duplicate address...
PC1 : 10.2.1.1 255.255.255.0 gateway 10.2.1.254


PC1> ping 8.8.8.8

84 bytes from 8.8.8.8 icmp_seq=1 ttl=114 time=280.912 ms
84 bytes from 8.8.8.8 icmp_seq=2 ttl=114 time=28.266 ms
84 bytes from 8.8.8.8 icmp_seq=3 ttl=114 time=29.583 ms
84 bytes from 8.8.8.8 icmp_seq=4 ttl=114 time=29.288 ms
84 bytes from 8.8.8.8 icmp_seq=5 ttl=114 time=30.304 ms


PC1> trace 8.8.8.8
trace to 8.8.8.8, 8 hops max, press Ctrl+C to stop
 1   10.2.1.254   4.009 ms  2.009 ms  1.297 ms
 2   192.168.122.1   2.657 ms  1.920 ms  2.578 ms
 3   10.0.3.2   3.037 ms  3.345 ms  4.286 ms
 4     *  *  *
 5     *  *  *
 6     *  *  *
 7     *  *  *
 8     *  *  *



IOU1#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0050.7966.6800    DYNAMIC     Et0/0
   1    0800.275b.7fd6    DYNAMIC     Et0/1
Total Mac Addresses for this criterion: 2



DEVICE=enp0s3
NAME=dhcp

ONBOOT=yes
BOOTPROTO=static

IPADDR=10.2.1.253
NETMASK=255.255.255.0
GATEWAY=10.2.1.254