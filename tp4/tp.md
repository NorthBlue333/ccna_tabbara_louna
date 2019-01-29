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
  La ligne 10.33.2.17 a été ajoutée. Le PC se connecte à internet, il a donc besoin d'une passerelle.
  
  ## Manip 4
  `sudo ip neigh flush all` sur toutes les machines.
 Sur **client1** :
```
ip neigh show
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:0b REACHABLE
```
  Activation de la carte NAT sur Virtual Box et redémarrage de la VM.
 ```
 curl google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
 ```
*On a bien un accès internet sur la VM*
```
ip neigh show
10.0.3.2 dev enp0s8 lladdr 52:54:00:12:35:02 DELAY
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:0b REACHABLE
```
L'adresse `10.0.3.2` est celle du PC qui est connecté en NAT à la VM et permet l'accès à internet.

**Récapitulatif**


| Machine | net1         | net2         | MACnet1             | MACnet2             |
|---------|--------------|--------------|---------------------|---------------------|
| client1 |  `10.0.1.10` |       X      | `08:00:27:d3:57:10` |          X          |
| router1 | `10.1.0.254` | `10.2.0.254` | `08:00:27:bb:9a:30` | `08:00:27:c7:a9:68` |
| server1 |       X      |  `10.2.0.10` |          X          | `08:00:27:8c:a8:a9` |

# Wireshark
## A. Interception d'ARP et ping
**Sur router1** `sudo tcpdump -i enp0s8 -w ping.pcap` *ping depuis client1 vers server1*

Ouverture d'un accès navigateur internet pour récupérer le fichier [Wireshark ping.pcap](ping.pcap) avec la commande `python2 -m SimpleHTTPServer 8888`

## B. netcat
**Sur server1** `firewall-cmd --add-port=5454/tcp --permanent`, `nc -l 5454`

**Sur client1** `nc server1 5454`

**Sur router1** `sudo tcpdump -i enp0s9 -w nc.pcap`

[Wireshark nc.pcap](nc.pcap)

*fermeture du port firewall*

[Wireshark ncfirewall.pcap](ncfirewall.pcap)

On peut voir `Destination unreachable (host adminisrtatively prohibited)` sur Wireshark.

## C. Trafic HTTP
### Install et config
```
sudo yum install -y nginx
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --reload
sudo systemctl start nginx
```
*curl depuis router1*
```
curl server1
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">

<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
    <head>
        <title>Test Page for the Nginx HTTP Server on Fedora</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
        <style type="text/css">
            /*<![CDATA[*/
            body {
                background-color: #fff;
                color: #000;
                font-size: 0.9em;
                font-family: sans-serif,helvetica;
                margin: 0;
                padding: 0;
            }
            :link {
                color: #c00;
            }
            :visited {
                color: #c00;
            }
            a:hover {
                color: #f50;
            }
            h1 {
                text-align: center;
                margin: 0;
                padding: 0.6em 2em 0.4em;
                background-color: #294172;
                color: #fff;
                font-weight: normal;
                font-size: 1.75em;
                border-bottom: 2px solid #000;
            }
            h1 strong {
                font-weight: bold;
                font-size: 1.5em;
            }
            h2 {
                text-align: center;
                background-color: #3C6EB4;
                font-size: 1.1em;
                font-weight: bold;
                color: #fff;
                margin: 0;
                padding: 0.5em;
                border-bottom: 2px solid #294172;
            }
            hr {
                display: none;
            }
            .content {
                padding: 1em 5em;
            }
            .alert {
                border: 2px solid #000;
            }

            img {
                border: 2px solid #fff;
                padding: 2px;
                margin: 2px;
            }
            a:hover img {
                border: 2px solid #294172;
            }
            .logos {
                margin: 1em;
                text-align: center;
            }
            /*]]>*/
        </style>
    </head>

    <body>
        <h1>Welcome to <strong>nginx</strong> on Fedora!</h1>

        <div class="content">
            <p>This page is used to test the proper operation of the
            <strong>nginx</strong> HTTP server after it has been
            installed. If you can read this page, it means that the
            web server installed at this site is working
            properly.</p>

            <div class="alert">
                <h2>Website Administrator</h2>
                <div class="content">
                    <p>This is the default <tt>index.html</tt> page that
                    is distributed with <strong>nginx</strong> on
                    Fedora.  It is located in
                    <tt>/usr/share/nginx/html</tt>.</p>

                    <p>You should now put your content in a location of
                    your choice and edit the <tt>root</tt> configuration
                    directive in the <strong>nginx</strong>
                    configuration file
                    <tt>/etc/nginx/nginx.conf</tt>.</p>

                </div>
            </div>

            <div class="logos">
                <a href="http://nginx.net/"><img
                    src="nginx-logo.png"
                    alt="[ Powered by nginx ]"
                    width="121" height="32" /></a>

                <a href="http://fedoraproject.org/"><img
                    src="poweredby.png"
                    alt="[ Powered by Fedora ]"
                    width="88" height="31" /></a>
            </div>
        </div>
    </body>
</html>
```

[Wireshark http.pcap](http.pcap)


On voit :
* La requête ARP (Who has `10.2.0.10? Tell 10.0.2.254` et `10.2.0.10 is at 08:00:27:8c:a8:a9`)
* Le trafic HTTP qui passe par des trames TCP, depuis `10.1.0.10` (client1) vers `10.2.0.10` (server1) : c'est la demande de la page HTML (demande la synchronisation)
* De même, mais de server1 vers client1, où il répond en acceptant la synchronisation
* Le fichier HTML et demandé par client1 (trame HTTP) et renvoyé par server1 (trame HTTP `text/html`)
* Les deux VM échangent pour terminer la synchronisation
