### PARTIE 1

## B. Ajout des Routes vers les LAN

3. Tests de ping :
   - Ping entre PC1 du LAN1 et LAN2.
   ```
  PC1> ping 10.3.2.1

*10.3.1.254 icmp_seq=1 ttl=255 time=65.428 ms 
*10.3.1.254 icmp_seq=2 ttl=255 time=18.268 ms 
*10.3.1.254 icmp_seq=3 ttl=255 time=16.511 ms 
*10.3.1.254 icmp_seq=4 ttl=255 time=10.806 ms 
   ```
4. Capture Wireshark :
   - On capture le trafic entre les routeurs pendant le ping.
   Et o nverifie que :
     - Les adresses MAC dans les trames sont celles des routeurs.
     - Les adresses IP des paquets sont celles des clients.

5. Afficher les adresses MAC :
     ```
     R1#show interfaces
FastEthernet0/0 is up, line protocol is up
  Hardware is Gt96k FE, address is c401.0534.0000 (bia c401.0534.0000)

R2#show interfaces
FastEthernet0/0 is up, line protocol is up
  Hardware is Gt96k FE, address is c402.0566.0000 (bia c402.0566.0000)
     ```


### **C. Accès Internet**

1. **Accès Internet sur R1 :**
   - On test l'accès à Internet depuis R1 :
     ```
     R1#ping 8.8.8.8

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 10/12/15 ms

     ```

3. **Accès Internet pour LAN1 :**
   - pareil depuis un client de LAN1 :
     ```
     PC1> ping 8.8.8.8

84 bytes from 8.8.8.8 icmp_seq=1 ttl=119 time=25.631 ms
84 bytes from 8.8.8.8 icmp_seq=2 ttl=119 time=24.872 ms
84 bytes from 8.8.8.8 icmp_seq=3 ttl=119 time=25.018 ms
84 bytes from 8.8.8.8 icmp_seq=4 ttl=119 time=24.765 ms
84 bytes from 8.8.8.8 icmp_seq=5 ttl=119 time=25.109 ms

--- 8.8.8.8 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4004ms
rtt min/avg/max = 24.765/25.079/25.631 ms

     ```

4. **Accès Internet pour LAN2 :**
   - Testons depuis un client de LAN2 :
     ```
    PC3> ping 8.8.8.8

84 bytes from 8.8.8.8 icmp_seq=1 ttl=119 time=19.347 ms
84 bytes from 8.8.8.8 icmp_seq=2 ttl=119 time=19.458 ms
84 bytes from 8.8.8.8 icmp_seq=3 ttl=119 time=19.612 ms
84 bytes from 8.8.8.8 icmp_seq=4 ttl=119 time=19.521 ms
84 bytes from 8.8.8.8 icmp_seq=5 ttl=119 time=19.486 ms

--- 8.8.8.8 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4003ms
rtt min/avg/max = 19.347/19.485/19.612 ms

     ```

### PARTIE 2

- A. VLANs
Tests de ping
```
 PC1> ping 10.3.1.2
84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=11.044 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=8.190 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=10.627 ms
```

- B. Routeur
 Tests de ping du routeur qui ping tout le monde
 ```
 R1# ping 10.3.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/3/5 ms
```
```
R1# ping 10.3.1.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.1.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/4/6 ms
```
Maintenant du LAN1 et LAN2 qui se ping
```
PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.3.1.1/24
GATEWAY     : 10.3.1.254
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 10008
RHOST:PORT  : 127.0.0.1:10009
MTU:        : 1500

PC1> ping 10.3.1.2
84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=8.753 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=10.882 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=8.539 ms
```
Ensuire du routeur qui ping Internet
```
R1#ping 8.8.8.8

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 60/83/96 ms
```
Et enfin qu'un clinet a un accès Internet
```
PC1> ping 8.8.8.8

84 bytes from 8.8.8.8 icmp_seq=1 ttl=117 time=21.3 ms
84 bytes from 8.8.8.8 icmp_seq=2 ttl=117 time=22.1 ms
84 bytes from 8.8.8.8 icmp_seq=3 ttl=117 time=20.8 ms
84 bytes from 8.8.8.8 icmp_seq=4 ttl=117 time=21.5 ms
84 bytes from 8.8.8.8 icmp_seq=5 ttl=117 time=22.0 ms

PC1>
```

### PARTIE 3
 - 1. DHCP
 Prouvez avec un VPCS
 ```
 PC1> ip dhcp

DHCP request sent, waiting for reply ...
DHCP reply received from 10.3.3.3
IP: 10.3.3.100/24, Gateway: 10.3.3.1, DNS: 1.1.1.1

PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.3.3.100/24
GATEWAY     : 10.3.3.1
DNS         : 1.1.1.1
MAC         : 00:50:79:66:68:00
LPORT       : 10008
RHOST:PORT  : 127.0.0.1:10009
MTU:        : 1500

PC1> ping efrei.fr

84 bytes from 93.184.216.34 icmp_seq=1 ttl=58 time=12.45 ms
84 bytes from 93.184.216.34 icmp_seq=2 ttl=58 time=13.22 ms
```
2. DNS
- Tests résolutions DNS

3. HTTP
- Preuve avec un client
```
curl http://10.3.3.3

<!DOCTYPE html>
<html>
<head>
    <title>Welcome to nginx!</title>
</head>
<body>
    <h1>Welcome to nginx!</h1>
    <p>If you see this page, the nginx web server is successfully installed and working.</p>
</body>
</html>
```

