# Préparation du lab
*installation des VMs faite en cours*

Mise en place du réseau avec le drag&drop.

## Checklist IP VMs
- [X] Définition des IPs statiques
Sur chaque VM :
* client1
* client2
* server1

`sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3` et dans le fichier :
```
NAME=enp0s3
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.5.2.10 OU 10.5.2.11 OU 10.5.1.10 (client1, client2, server1)
NETMASK=255.255.255.0
```
puis
```
sudo ifdown enp0s3
sudo ifup enp0s3
```

- [X] La connexion SSH doit être fonctionnelle (voir tableau plus bas avec récap, adresse IP SSH pour reminder)
  - une fois fait, vous avez vos trois fenêtres SSH ouvertes, une dans chaque machine
- [X] Définition du nom de domaine (pour les trois VMs, exemple seulement pour la VM client1)
`echo 'client1.tp5.b1' | sudo tee /etc/hostname`

`sudo nano /etc/hosts` :
```
10.5.2.11 client2 client2.tp5.b1
10.5.1.10 server1 server1.tp5.b1
```
