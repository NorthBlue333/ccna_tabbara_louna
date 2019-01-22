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
