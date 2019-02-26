# Création du lab
On a donc :
* 5 routers c3600
* 2 clients sous CentOs
* 1 server sous CentOs

Le tout réparti dans les 3 zones comme dans le schéma :
![Schéma](https://github.com/It4lik/B1-Reseau-2018/blob/master/tp/6/pic/lab-2-topo.png)

Les cartes Host-Only pour le SSH sont configurées :


| Machine |       SSH      |
|:-------:|:--------------:|
| client1 | `192.168.98.3` |
| client2 | `192.168.98.4` |
| server1 | `192.168.98.5` |

Machines | `10.6.100.0/30` | `10.6.100.4/30` | `10.6.100.8/30` | `10.6.100.12/30` | `10.6.101.0/30` | `10.6.201.0/24` | `10.6.202.0/24`
--- | --- | --- | --- | --- | --- | --- | --- 
`r1.tp6.b1` | `10.6.100.1` | `10.6.100.5` | - | - | - | - | `10.6.202.254`
`r2.tp6.b1` | `10.6.100.2` | - |  `10.6.100.9` | - | - | - | -
`r3.tp6.b1` | - | - | `10.6.100.10` | `10.6.100.13` | `10.6.101.1` | - | -
`r4.tp6.b1` | - |  `10.6.100.6` | - | `10.6.100.14` | - | - | -
`r5.tp6.b1` | - | - | - | - |  `10.6.101.2` |  `10.6.201.254` | -
`client1.tp6.b1` | - | - | - | - | - |  `10.6.201.10` | -
`client2.tp6.b1` | - | - | - | - | - |  `10.6.201.11` | -
`server1.tp6.b1` | - | - | - | - | - | - | `10.6.202.10`

## Checklist routers
- [X] IPs statiques :
*Depuis le terminal fourni par GNS3*

**Par exemple pour r1** (selon le tableau ci-dessus)
 ```
 config t
 interface ethernet 0/0 //interface sur laquelle on veut définir l'IP
 ip address 10.6.202.254 255.255.255.0 //IP et netmask
 no shut //allumer la carte
 exit
 interface ethernet 0/2
 ip address 10.6.100.1 255.255.255.252
 no shut
 exit
 interface ethernet 0/3
 ip address 10.6.100.5 255.255.255.252
 no shut
 exit
 exit
 wr //copy running-config startup-config
 ```

- [X] hostnames :
`r1.tp6.b1`, `r2.tp6.b1`, `r3.tp6.b1`, `r4.tp6.b1` et `r5.tp6.b1`
```
conf t
hostname [un de ceux au-dessus en fonction]
wr
```

## Checklist VMs
- [X] IPs statiques :

**Par exemple pour client1** (selon le tableau ci-dessus)
```
sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3

NAME=enp0s3
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.6.201.10
NETMASK=255.255.255.0 //masque réseau
GATEWAY=10.6.201.254 //passerelle

sudo ifdown enp0s3
sudo ifup enp0s3
```

- [X] hostnames & fichiers hosts : *(permanent)*
`client1.tp6.b1`, `client2.tp6.b1` et `server1.tp6.b1`

`echo '[hostname ci-dessus].tp6.b1' | sudo tee /etc/hostname`

Dans le fichier host pour le client1 par exemple :

```
sudo nano /etc/hosts
10.6.201.11 client2 client2.tp6.b1
10.6.202.10 server1 server1.tp6.b1
```
