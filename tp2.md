## I. Routage

# Configuration de router.tp2.efrei

 On le setup pour qu'elle recupere une ip avec dhcp.

 ```
  
[boubou0b002-10 ~]$ ping google.com
PING google.com (172.217.18.206) 56(84) bytes of data.
64 bytes from par10s38-in-f14.1e100.net (172.217.18.206): icmp_seq=1 ttl=116 time=21.1 ms
64 bytes from ham02s14-in-f206.1e100.net (172.217.18.206): icmp_seq=2 ttl=116 time=33.8 ms
64 bytes from par10s38-in-f14.1e100.net (172.217.18.206): icmp_seq=3 ttl=116 time=26.7 ms
64 bytes from ham02s14-in-f206.1e100.net (172.217.18.206): icmp_seq=4 ttl=116 time=30.1 ms
^C
--- google.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3008ms
rtt min/avg/max/mdev = 21.120/27.934/33.771/4.661 ms
[boubou0b002-10 ~]$

 ```

# Configuration de node1.tp2.efrei

```

boubou@pc-108 ~]$ sudo ip addr add 10.2.1.1/24 dev enp0s3
boubou@pc-108 ~]$ sudo ip link set enp0s3 up
boubou@pc-108 ~]$ ip addr show dev enp0s3
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:cf:98:31 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.29/24 brd 192.168.1.255 scope global dynamic noprefixroute enp0s3
       valid_lft 85759sec preferred_lft 85759sec
    inet 10.2.1.1/24 scope global enp0s3
       valid_lft forever preferred_lft forever
    inet6 2a01:cb19:63d:d700:a00:27ff:fecf:9831/64 scope global dynamic noprefixroute
       valid_lft 1798sec preferred_lft 598sec
    inet6 fe80::a00:27ff:fecf:9831/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
boubou@pc-108 ~]$

```

```
boubou@pc-108 ~]$ sudo ip route add default via 10.2.1.254 dev enp0s3

```

boubou@pc-108 ~]$ ip route show

default via 10.2.1.254 dev enp0s3 
10.2.1.0/24 dev enp0s3 proto kernel scope link src 10.2.1.1 


```

```
boubou@pc-108 ~]$ ping 8.8.8.8

PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=118 time=10.3 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=118 time=10.2 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=118 time=10.5 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=118 time=10.4 ms

--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3002ms

```
boubou@pc-108 ~]$ sudo apt update && sudo apt install traceroute -y

```
boubou@pc-108 ~]$ traceroute 8.8.8.8

traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  10.2.1.254 (10.2.1.254)  0.712 ms  0.702 ms  0.692 ms
 2  192.168.0.1 (192.168.0.1)  1.341 ms  1.327 ms  1.318 ms
 3  8.8.8.8 (8.8.8.8)  20.031 ms  20.021 ms  20.011 ms


```

```
# Afficher la CAM Table du switch

```

IOU1#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0800.27f4.b75c    DYNAMIC     Et0/1
Total Mac Addresses for this criterion: 1
IOU1#

```

## II. Serveur DHCP

# Install et conf du serveur DHCP sur dhcp.tp2.efrei

```

boubou@pc-108 ~]$ sudo ip route add default via 10.2.1.254 dev enp0s3


```

On installe un serveur DHCP
Dans le fichier de configuration DHCP (/etc/dhcp/dhcpd.conf) on inclue les informations suivantes :

```

subnet 10.2.1.0 netmask 255.255.255.0 {
    range 10.2.1.100 10.2.1.200;  # Plage d'IP disponibles
    option routers 10.2.1.254;    # Passerelle
    option domain-name-servers 1.1.1.1;  # Serveur DNS
}

```

On redemarre le service DHCP :

```

sudo systemctl restart isc-dhcp-server
sudo systemctl enable isc-dhcp-server

```

# Test du DHCP sur node1.tp2.efrei

ON supprime toute configuration IP statique :

```

boubou@pc-108 ~]$ sudo nmcli con del enp0s3

Connection 'enp0s3' (a1b2c3d4-e5f6-7890-abcd-ef1234567890) successfully deleted.


```

Configuration de l'interface pour obtenir une IP via DHCP :

```

boubou@pc-108 ~]$ sudo dhclient enp0s3


```

```

boubou@pc-108 ~]$ ip addr show dev enp0s3

2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:cf:98:31 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.100/24 brd 192.168.1.255 scope global dynamic enp0s3
       valid_lft 86400sec preferred_lft 86400sec
    inet6 fe80::a00:27ff:fecf:9831/64 scope link 
       valid_lft forever preferred_lft forever

```
boubou@pc-108 ~]$ ip route show

default via 192.168.1.1 dev enp0s3 proto dhcp metric 100 
192.168.1.0/24 dev enp0s3 proto kernel scope link src 192.168.1.100 metric 100

```

boubou@pc-108 ~]$ ping 8.8.8.8

PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=118 time=10.1 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=118 time=10.2 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=118 time=10.3 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=118 time=10.4 ms


```

## III. ARP

# Affichez la table ARP de router.tp2.efrei

```

show mac address-table
show ip arp

```

Si des entrées manquent, on fais  un ping depuis le routeur vers les machines :

# Envoi d'une trame ARP arbitraire

```

sudo arping -c 1 -S 10.2.1.254 -D 10.2.1.1 -I enp0s3

```

# Mettre en place un ARP MITM

Lancons l'attaque :

```

sudo arpspoof -i enp0s3 -t 10.2.1.x 10.2.1.254
sudo arpspoof -i enp0s3 -t 10.2.1.254 10.2.1.x

```

```
sudo sysctl -w net.ipv4.ip_forward=1

```