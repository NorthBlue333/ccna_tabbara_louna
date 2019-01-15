# I. CentOS
## Désactivation SELLinux
```
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   permissive
Mode from config file:          permissive
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      31
```
## Joujou
* **Internet :**
```
wget google.com
--2019-01-15 15:47:30--  http://google.com/
Resolving google.com (google.com)... 216.58.213.174, 2a00:1450:4007:811::200e
Connecting to google.com (google.com)|216.58.213.174|:80... connected.
HTTP request sent, awaiting response... 301 Moved Permanently
Location: http://www.google.com/ [following]
--2019-01-15 15:47:30--  http://www.google.com/
Resolving www.google.com (www.google.com)... 216.58.198.196, 2a00:1450:4007:80a::2004
Connecting to www.google.com (www.google.com)|216.58.198.196|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Saving to: ‘index.html’

    [ <=>                                                                           ] 11,313      --.-K/s   in 0.006s

2019-01-15 15:47:30 (1.91 MB/s) - ‘index.html’ saved [11313]
```
* **Connexion VM et PC :**
    * Depuis la VM :
```
ping 192.168.102.1
PING 192.168.102.1 (192.168.102.1) 56(84) bytes of data.
64 bytes from 192.168.102.1: icmp_seq=1 ttl=128 time=0.243 ms
64 bytes from 192.168.102.1: icmp_seq=2 ttl=128 time=0.327 ms
```
     * Depuis le PC :
```
ping 192.168.102.10

Envoi d’une requête 'Ping'  192.168.102.10 avec 32 octets de données :
Réponse de 192.168.102.10 : octets=32 temps<1ms TTL=64
Réponse de 192.168.102.10 : octets=32 temps<1ms TTL=64
```
* **Table de routage de la VM :**
```
default via 10.0.2.2 dev enp0s3 proto dhcp metric 101
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 101
192.168.102.0/24 dev enp0s8 proto kernel scope link src 192.168.102.10 metric 100
```
La première ligne indique l'adresse IP utilisée par la VM pour communiquer sur un réseau externe (comme le deuxième). Le deuxième est le réseau permettant d'utiliser internet à travers le PC. La troisième permet la connexion entre le PC et la VM (Virtual Host).

* **Table de routage du PC :**
```
IPv4 Table de routage
===========================================================================
Itinéraires actifs :
Destination réseau    Masque réseau  Adr. passerelle   Adr. interface Métrique
          0.0.0.0          0.0.0.0    192.168.112.1    192.168.112.2    281
          0.0.0.0          0.0.0.0      10.33.3.253       10.33.0.79     35
        10.33.0.0    255.255.252.0         On-link        10.33.0.79    291
       10.33.0.79  255.255.255.255         On-link        10.33.0.79    291
      10.33.3.255  255.255.255.255         On-link        10.33.0.79    291
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
     192.168.56.0    255.255.255.0         On-link      192.168.56.1    281
     192.168.56.1  255.255.255.255         On-link      192.168.56.1    281
   192.168.56.255  255.255.255.255         On-link      192.168.56.1    281
    192.168.101.0    255.255.255.0    192.168.112.1    192.168.112.2     26
    192.168.102.0    255.255.255.0         On-link     192.168.102.1    281 -- permet de communiquer avec la VM
    192.168.102.1  255.255.255.255         On-link     192.168.102.1    281 -- permet de communiquer avec la VM
  192.168.102.255  255.255.255.255         On-link     192.168.102.1    281
    192.168.112.0    255.255.255.0         On-link     192.168.112.2    281
    192.168.112.2  255.255.255.255         On-link     192.168.112.2    281
  192.168.112.255  255.255.255.255         On-link     192.168.112.2    281
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     192.168.112.2    281
        224.0.0.0        240.0.0.0         On-link     192.168.102.1    281
        224.0.0.0        240.0.0.0         On-link      192.168.56.1    281
        224.0.0.0        240.0.0.0         On-link        10.33.0.79    291
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     192.168.112.2    281
  255.255.255.255  255.255.255.255         On-link     192.168.102.1    281
  255.255.255.255  255.255.255.255         On-link      192.168.56.1    281
  255.255.255.255  255.255.255.255         On-link        10.33.0.79    291
===========================================================================
```
* **Dig :**
    * Google :
    ```
    ;; ANSWER SECTION:
    google.com.             46      IN      A       216.58.204.110
    ```
    * Ynov :
    ```
    ;; ANSWER SECTION:
    ynov.com.               10799   IN      A       217.70.184.38
    ```

# III. Routage statique
* PC1 : `192.168.112.1`
* PC2 : `192.168.112.2`
* VM1 : `192.168.101.10`
* VM2 : `192.168.102.10`

* **PC1 à PC2 :**
```ping 192.168.112.1
Envoi d’une requête 'Ping'  192.168.112.1 avec 32 octets de données :
Réponse de 192.168.112.1 : octets=32 temps=4 ms TTL=128
Réponse de 192.168.112.1 : octets=32 temps=4 ms TTL=128
Réponse de 192.168.112.1 : octets=32 temps=4 ms TTL=128
Réponse de 192.168.112.1 : octets=32 temps=4 ms TTL=128
```
* **PC à VM**
```ping 192.168.102.10
Envoi d’une requête 'Ping'  192.168.102.10 avec 32 octets de données :
Réponse de 192.168.102.10 : octets=32 temps<1ms TTL=64
Réponse de 192.168.102.10 : octets=32 temps<1ms TTL=64
Réponse de 192.168.102.10 : octets=32 temps<1ms TTL=64
Réponse de 192.168.102.10 : octets=32 temps<1ms TTL=64
```

*Ajout de la route sur PC2*
`ip route add 192.168.101.0/24 mask 255.255.255.0 192.168.112.1`

*Ajout de la route sur VM2*
`ip route add 192.168.101.0/24 via 192.168.102.1 dev enp0s8`

*Hosts sur PC2*
```
127.0.0.1 pc2.tp3.b1
192.168.112.1 pc1.tp3.b1
192.168.102.10 vm2.tp3.b1
192.168.101.10 vm1.tp3.b1
```

*Hosts sur VM2*
```
127.0.0.1 vm2.tp3.b1
192.168.112.1 pc1.tp3.b1
192.168.102.1 pc2.tp3.b1
192.168.101.10 vm1.tp3.b1
```

**Résultats des ping depuis la VM :**
```
ping pc2.tp3.b1
PING pc2 (192.168.102.1) 56(84) bytes of data.
64 bytes from pc2 (192.168.102.1): icmp_seq=1 ttl=128 time=0.326 ms
```
```
ping pc1.tp3.b1
PING pc1 (192.168.112.1) 56(84) bytes of data.
64 bytes from pc1 (192.168.112.1): icmp_seq=1 ttl=127 time=5.72 ms
```
```
ping vm1.tp3.b1
PING vm1 (192.168.101.10) 56(84) bytes of data.
64 bytes from vm1 (192.168.101.10): icmp_seq=1 ttl=62 time=5.04 ms
```
**Résultats des ping depuis le PC :**
```
ping vm2.tp3.b1

Envoi d’une requête 'ping' sur vm2.tp3.b1 [192.168.102.10] avec 32 octets de données :
Réponse de 192.168.102.10 : octets=32 temps<1ms TTL=64
```
```
ping pc1.tp3.b1

Envoi d’une requête 'ping' sur pc1.tp3.b1 [192.168.112.1] avec 32 octets de données :
Réponse de 192.168.112.1 : octets=32 temps=4 ms TTL=128
```
```
ping vm1.tp3.b1

Envoi d’une requête 'ping' sur vm1.tp3.b1 [192.168.101.10] avec 32 octets de données :
Réponse de 192.168.101.10 : octets=32 temps=4 ms TTL=63
```
