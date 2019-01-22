# Mise en place
*Création des VMs et écriture des fichiers hosts OK*

# Routage statique
## router1
IP fowarding :
```
sudo sysctl -w net.ipv4.conf.all.forwarding=1
net.ipv4.conf.all.forwarding = 1
```
Désactivation du firewall :
```
sudo systemctl disable firewalld
Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
```
Routes :
```
ip route show
10.1.0.0/24 dev enp0s3 proto kernel scope link src 10.1.0.254 metric 100
10.2.0.0/24 dev enp0s8 proto kernel scope link src 10.2.0.254 metric 101
```

## client1

`sudo nano /etc/sysconfig/network-scripts/route-enp0s3` : `10.2.0.0/24 via 10.1.0.254 dev enp0s3`

## server1

`sudo nano /etc/sysconfig/network-scripts/route-enp0s3` : `10.1.0.0/24 via 10.2.0.254 dev enp0s3`

## test
```PING client1 (10.1.0.10) 56(84) bytes of data.
64 bytes from client1 (10.1.0.10): icmp_seq=1 ttl=63 time=0.714 ms
64 bytes from client1 (10.1.0.10): icmp_seq=2 ttl=63 time=0.897 ms

traceroute to client1 (10.1.0.10), 30 hops max, 60 byte packets
 1  router1 (10.2.0.254)  0.359 ms  0.286 ms  0.374 ms
 2  router1 (10.2.0.254)  0.330 ms !X  0.162 ms !X  0.302 ms !X
```
```
PING server1 (10.2.0.10) 56(84) bytes of data.
64 bytes from server1 (10.2.0.10): icmp_seq=1 ttl=63 time=0.869 ms
64 bytes from server1 (10.2.0.10): icmp_seq=2 ttl=63 time=0.891 ms

traceroute to server1 (10.2.0.10), 30 hops max, 60 byte packets
 1  router1 (10.1.0.254)  0.455 ms  0.324 ms  0.184 ms
 2  router1 (10.1.0.254)  0.095 ms !X  0.229 ms !X  0.278 ms !X
```

# ARP
## Manip 1
*Après un sudo ip neigh flush all*
client1
`10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:0b REACHABLE`
server1
`10.2.0.1 dev enp0s3 lladdr 0a:00:27:00:00:0e REACHABLE`
Ce sont les connexions Virtual Host de VirtualBox
*Après un ping*
client1
```
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:0b REACHABLE
10.1.0.254 dev enp0s3 lladdr 08:00:27:bb:9a:30 REACHABLE
```
server1
```
10.2.0.1 dev enp0s3 lladdr 0a:00:27:00:00:0e DELAY
10.2.0.254 dev enp0s3 lladdr 08:00:27:c7:a9:68 DELAY
```
La ligne ajoutée est celle du routeur qui permet l'accès à l'autre réseau

## Manip 2
`10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:0b REACHABLE`
De même, c'est la connexion Virtual Host
```
10.1.0.10 dev enp0s3 lladdr 08:00:27:d3:57:10 REACHABLE
10.2.0.10 dev enp0s8 lladdr 08:00:27:8c:a8:a9 REACHABLE
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:0b DELAY
```
Ce sont les deux route pour client1 et server1
