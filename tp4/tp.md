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
