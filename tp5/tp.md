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
## Checklist IP Routeurs
Le réseau sur lequel les router sont est en /30 car on n'a pas besoin de plus de 2 machines sur le réseau.
- [X] Définition des IPs statiques
router1 :
```
show ip int br
conf t
interface ethernet 0/0
ip address 10.5.1.254 255.255.255.0
no shut
exit
interface ethernet 0/3
ip address 10.5.3.1 255.255.255.252
no shut
exit
exit
```
router2 :
```
show ip int br
conf t
interface ethernet 0/0
ip address 10.5.2.254 255.255.255.0
no shut
exit
interface ethernet 0/3
ip address 10.5.3.2 255.255.255.252
no shut
exit
exit
```
- [X] Définition du nom de domaine
router1 :
```
conf t
hostname router1.tp5.b1
exit
```
router2 :
```
conf t
hostname router2.tp5.b1
exit
```

| Machine |     net1     |     net2     |    net12   |       SSH      |
|:-------:|:------------:|:------------:|:----------:|:--------------:|
| client1 |       X      |  `10.5.2.10` |      X     | `192.168.98.4` |
| client2 |       X      |  `10.5.2.11` |      X     | `192.168.98.3` |
| router1 | `10.5.1.254` |       X      | `10.5.3.1` |        X       |
| router2 |       X      | `10.5.2.254` | `10.5.3.2` |        X       |
| server1 |  `10.5.1.10` |       X      |      X     | `192.168.98.5` |


## Checklist routes
Fichiers hosts fait en même temps que le hostname

- [X] router1.tp5.b1
```
conf t
ip route 10.5.2.0 255.255.255.0 10.5.3.2
```
- [X] router2.tp5.b1
```
conf t
ip route 10.5.1.0 255.255.255.0 10.5.3.2
```
- [X] server1.tp5.b1
```
sudo nano /etc/sysconfig/network-script/route-enp0s3
(dans le fichier) 10.5.2.0/24 via 10.5.1.254 dev enp0s3
sudo systemctl restart network
```
- [X] client1.tp5.b1
```
sudo nano /etc/sysconfig/network-script/route-enp0s3
(dans le fichier) 10.5.1.0/24 via 10.5.2.254 dev enp0s3
sudo systemctl restart network
```
- [X] client2.tp5.b1
```
sudo nano /etc/sysconfig/network-script/route-enp0s3
(dans le fichier) 10.5.1.0/24 via 10.5.2.254 dev enp0s3
sudo systemctl restart network
```

**Depuis client1**
```
ping server1
PING server1 (10.5.1.10) 56(84) bytes of data.
64 bytes from server1 (10.5.1.10): icmp_seq=2 ttl=62 time=29.5 ms
64 bytes from server1 (10.5.1.10): icmp_seq=3 ttl=62 time=26.6 ms
```
**Depuis client2**
```
ping server1
PING server1 (10.5.1.10) 56(84) bytes of data.
64 bytes from server1 (10.5.1.10): icmp_seq=1 ttl=62 time=35.2 ms
64 bytes from server1 (10.5.1.10): icmp_seq=2 ttl=62 time=28.6 ms
```

# DHCP
client2 renommée dhcp-net2

Ajout de la carte NAT via virtual box, démarrage de la machine, `sudo yum install -y dhcp`, shutdown de la VM, rallumage dans GNS3.

Dans le fichier `sudo nano /etc/dhcp/dhcpd.conf` de dhcp-net2 :

```
# dhcpd.conf

# option definitions common to all supported networks
option domain-name "tp5.b1";

default-lease-time 600; //durée du bail par défaut
max-lease-time 7200; //durée maximum du bail

# If this DHCP server is the official DHCP server for the local
# network, the authoritative directive should be uncommented.
authoritative;

# Use this to send dhcp log messages to a different log file (you also
# have to hack syslog.conf to complete the redirection).
log-facility local7;

subnet 10.5.2.0 netmask 255.255.255.0 { //sous réseau et masque
  range 10.5.2.50 10.5.2.70; //plage d'adressage
  option domain-name "tp5.b1"; //nom du domain
  option routers 10.5.2.254; //IP du router
  option broadcast-address 10.5.2.255; //IP broadcast
}
```
puis `sudo systemctl enable dhcpd`

Sur client1 :

* IP dynamique
`sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3`

```
NAME=enp0s3
DEVICE=enp0s3

BOOTPROTO=dhcp
ONBOOT=yes
```
`sudo ifdown enp0s3 `et` sudo ifup enp0s3`
Résultat : `ip a`
```
enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:35:66:94 brd ff:ff:ff:ff:ff:ff
    inet 10.5.2.50/24 brd 10.5.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 598sec preferred_lft 598sec
    inet6 fe80::a00:27ff:fe35:6694/64 scope link
       valid_lft forever preferred_lft forever
```
* dhclient
```
sudo dhclient -v -r //release du bail
sudo dhclient -v

ip a
enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:35:66:94 brd ff:ff:ff:ff:ff:ff
    inet 10.5.2.51/24 brd 10.5.2.255 scope global dynamic enp0s3
       valid_lft 598sec preferred_lft 598sec
    inet6 fe80::a00:27ff:fe35:6694/64 scope link
```

* capture [lease.pcap](lease.pcap)
On voit bien le DORA

# Bonus

* server1 :
```
sudo yum install -y nginx
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --reload
sudo systemctl start nginx
```

  sur client1 :

```
curl 10.5.1.10
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
[toor@client1 ~]$ clear
[toor@client1 ~]$ curl server1
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

* clés RSA
A partir de [ce site](https://technique.arscenic.org/connexion-distante-au-serveur-ssh/article/securisation-ssh-poussee-authentification-par-cle-rsa).

sur client2 :
```
ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/toor/.ssh/id_rsa):
Created directory '/home/toor/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/toor/.ssh/id_rsa.
Your public key has been saved in /home/toor/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:mMacKc0IJQaQPqc4Cr4z/fON7ynpyhic/jwJB3k0G2A toor@client2.tp5.b1
The key's randomart image is:
+---[RSA 2048]----+
|+ooEo            |
|...o +           |
|. . o +          |
| o = O =         |
|. + = @ S        |
|+....+           |
|+..+o . .        |
|.+..=+ oo .      |
| .+oo**++=       |
+----[SHA256]-----+
ssh-copy-id -i ~/.ssh/id_rsa.pub toor@10.5.1.10
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/toor/.ssh/id_rsa.pub"
The authenticity of host '10.5.1.10 (10.5.1.10)' can't be established.
ECDSA key fingerprint is SHA256:REkWEazPn0G2b7jjN43oZxhLT+CjHIuNfgJTN/yhPTk.
ECDSA key fingerprint is MD5:04:88:86:00:46:f9:ee:cf:3e:4b:0a:b7:7d:d2:de:8b.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
toor@10.5.1.10's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'toor@192.168.98.5'"
and check to make sure that only the key(s) you wanted were added.
```

sur server1 :
```
service sshd restart
Redirecting to /bin/systemctl restart sshd.service
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to manage system services or units.
Authenticating as: toor
Password:
==== AUTHENTICATION COMPLETE ===
[toor@server1 ~]$ chmod 700 ~/.ssh && chmod 600 ./~ssh/*
chmod: cannot access ‘./~ssh/*’: No such file or directory
[toor@server1 ~]$ sudo nano /etc/ssh/sshd_config
[sudo] password for toor:
[toor@server1 ~]$
//ici on met PasswordAuthentication no

[toor@server1 ~]$ service sshd restart
```

On peut se connecter en ssh depuis client2 sans problème juste en tapant `ssh toor@10.5.1.10`, mais depuis client1 ça donne :
```
ssh toor@10.5.1.10
Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
```
