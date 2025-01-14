## TP 1 :

# I/

Déterminer l'adresse MAC de vos deux machines

```
    VPCS> show

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
VPCS1  0.0.0.0/0            0.0.0.0           00:50:79:66:68:00  20002  127.0.0.1:20003
       fe80::250:79ff:fe66:6800/64

    VPCS> show

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
VPCS1  0.0.0.0/0            0.0.0.0           00:50:79:66:68:01  20004  127.0.0.1:20005
       fe80::250:79ff:fe66:6801/64

```

- Définir une IP statique sur les deux machines

Sur le node1 => ip 10.1.1.1/24 et sur le node2 => ip 10.1.1.2/24
Avec show ip on voit le que le changement est effectif.


-Effectuer un ping d'une machine à l'autre

```

VPCS> ping 10.1.1.2

84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=1.990 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=2.390 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=1.040 ms
^C

```


- ARP

```

VPCS> show arp

00:50:79:66:68:01  10.1.1.2 expires in 115 seconds

```


# II/

- Déterminer l'adresse MAC de vos trois machines

```

VPCS> show

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
VPCS1  10.1.1.3/24          0.0.0.0           00:50:79:66:68:02  20010  127.0.0.1:20011
       fe80::250:79ff:fe66:6802/64

```

- Définir une IP statique sur les trois machines

ip => 10.1.1.3/24
Show ip pour voir le changement


Effectuer des ping d'une machine à l'autre

```

VPCS> ping 10.1.1.2

84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=3.180 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=1.813 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=1.174 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=3.376 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=3.810 ms
^C

VPCS> ping 10.1.1.3

84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=1.244 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=3.286 ms
84 bytes from 10.1.1.3 icmp_seq=3 ttl=64 time=3.227 ms
^C

VPCS> ping 10.1.1.3

84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=4.229 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=2.310 ms
84 bytes from 10.1.1.3 icmp_seq=3 ttl=64 time=1.887 ms
^C

```

# III/ SERVEUR DHCP

ping google.com

```
PING google.com (172.217.169.46): 56 data bytes
64 bytes from 172.217.169.46: icmp_seq=0 ttl=118 time=12.5 ms
64 bytes from 172.217.169.46: icmp_seq=1 ttl=118 time=12.7 ms
64 bytes from 172.217.169.46: icmp_seq=2 ttl=118 time=12.6 ms
64 bytes from 172.217.169.46: icmp_seq=3 ttl=118 time=12.9 ms

```



dnf-y install dhcp-server
systemctl enable --now dhcp

authoritative;
subnet 10.1.1.0 netmask 255.255.255.0 {
	range 10.1.1.10 10.1.1.50;
	option broadcast-address 10.1.1.1;
	option routers 10.1.1.1;
}

On fait un "dhcp" puis un show ip pour les 3


dnf install -y dnsmasq

port=0 
dhcp-range=10.1.1.210,10.1.1.250,255.255.255.0,12h
dhcp-authoritative
interface=enp0s3

puis dhcp sur les 3


