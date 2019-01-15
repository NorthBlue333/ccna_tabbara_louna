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
* **Connexion VM et PC**
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

# III. Routage statique
* **PC1 à PC2 :**
  * ```ping 192.168.112.1
       Envoi d’une requête 'Ping'  192.168.112.1 avec 32 octets de données :
       Réponse de 192.168.112.1 : octets=32 temps=4 ms TTL=128
       Réponse de 192.168.112.1 : octets=32 temps=4 ms TTL=128
       Réponse de 192.168.112.1 : octets=32 temps=4 ms TTL=128
       Réponse de 192.168.112.1 : octets=32 temps=4 ms TTL=128
    ```
* **PC à VM**
  * ```ping 192.168.102.10
       Envoi d’une requête 'Ping'  192.168.102.10 avec 32 octets de données :
       Réponse de 192.168.102.10 : octets=32 temps<1ms TTL=64
       Réponse de 192.168.102.10 : octets=32 temps<1ms TTL=64
       Réponse de 192.168.102.10 : octets=32 temps<1ms TTL=64
       Réponse de 192.168.102.10 : octets=32 temps<1ms TTL=64
    ```
* 
