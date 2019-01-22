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
 ## Manip 3
 ```
 arp -a
 Interface : 10.33.2.16 --- 0x5
  Adresse Internet      Adresse physique      Type
  10.33.0.54            bc-a8-a6-fd-da-7c     dynamique
  10.33.0.139           5c-c5-d4-8c-83-c7     dynamique
  10.33.1.198           68-07-15-41-b8-fb     dynamique
  10.33.2.17            70-c9-4e-75-e3-77     dynamique
  10.33.2.78            10-4a-7d-44-f3-a4     dynamique
  10.33.2.145           04-d3-b0-0e-73-a1     dynamique
  10.33.2.179           b4-d5-bd-ac-ca-ec     dynamique
  10.33.2.191           c0-b6-f9-77-77-a2     dynamique
  10.33.3.109           34-41-5d-ac-f4-9a     dynamique
  10.33.3.198           08-d4-0c-c7-20-04     dynamique
  10.33.3.253           00-12-00-40-4c-bf     dynamique
  10.33.3.254           94-0c-6d-84-50-c8     dynamique
  10.33.3.255           ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  230.0.0.1             01-00-5e-00-00-01     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.1.0.1 --- 0xb
  Adresse Internet      Adresse physique      Type
  10.1.0.10             08-00-27-d3-57-10     dynamique
  10.1.0.254            08-00-27-bb-9a-30     dynamique
  10.1.0.255            ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.2.0.1 --- 0xe
  Adresse Internet      Adresse physique      Type
  10.2.0.10             08-00-27-8c-a8-a9     dynamique
  10.2.0.255            ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  230.0.0.1             01-00-5e-00-00-01     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  ```
  ```
  arp -d *
  arp -a
Interface : 10.33.2.16 --- 0x5
  Adresse Internet      Adresse physique      Type
  10.33.3.253           00-12-00-40-4c-bf     dynamique
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.1.0.1 --- 0xb
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.2.0.1 --- 0xe
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique
  230.0.0.1             01-00-5e-00-00-01     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  ```
  puis
  ```
  arp -a
Interface : 10.33.2.16 --- 0x5
  Adresse Internet      Adresse physique      Type
  10.33.2.17            70-c9-4e-75-e3-77     dynamique
  10.33.3.253           00-12-00-40-4c-bf     dynamique
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.1.0.1 --- 0xb
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.2.0.1 --- 0xe
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique
  230.0.0.1             01-00-5e-00-00-01     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  ```
  La ligne 10.33.2.17 a été ajoutée
