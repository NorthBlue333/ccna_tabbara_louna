# Exploration locale en solo
## 1. Affichage d'informations sur la pile TCP/IP locale
### En ligne de commande
`ipconfig /all`
* **Interface WiFi :**
  * **Nom :** Killer Wireless-n/a/ac 1535 Wireless Network Adapter
  * **Adresse Mac :** 9C-B6-D0-0D-1B1B
  * **Adresse IP :** 10.33.3.238
  * **Adresse de réseau :** 10.33.0.0
  * **Adresse de broadcast :** 10.33.255.255

* **Interface Ethernet**
  *  **Nom :** Killer E2400 Gigabit Ethernet Controller
  *  **Adresse Mac :** 4C-CC-6A-7F-BE-EB
  *  **Adresse IP :** *Aucune car connecté en WiFi*

**Gateway :** Avec `ipconfig /all` : 10.33.3.253

### En graphique (sous Windows 10)
`Paramètres > Réseau et Internet > Afficher vos propriétés réseau`

### Questions
__A quoi sert la gateway d'Ingésup ?__
> Elle sert à accéder à internet à l'extérieur de l'école

## 2. Modifications des informations
**Première adresse IP :** 10.33.0.1
**Dernière adresse IP :** 10.33.3.252

`Paramètres > Réseau et Internet > Centre Réseau et partage > Clic droit sur la carte WiFi > IPv4 > Propriétés`
*Pas internet car adresse sûrement déjà utilisée*

# Exploration locale en duo
## 1. Modification d'adresse IP
`ipconfig /all` pour l'adresse IP vérifiée
`ping <ADRESSE IP DUO>` avec les pare-feu désactivés
> Adresse 192.168.1.3
> Réponse de 192.168.1.1 : octets=32 temps=9 ms TTL=128

Plus petit réseau : /30 (255.255.255.252) (car il faut une IP pour l'adresse de réseau)

## 2. Utilisation d'un des deux comme gateway
## 3. Petit chat privé
*Sur un bash debian*
* **Côté serveur :**
  * Utilisation de la commande `nc -l -p 8888`
* **Côté client :**
  * Utilisation de la commande `nc 192.168.1.1 8888`  

L'envoi et la réception de messages marche dans les deux sens (pare-feu désactivé)

## 4. Wireshark
* **Lors d'un ping :**
  * Les messages `Echo (ping) request id=0x003a, ...` et `Echo (ping) reply id=0x003a, ...` sont visibles
* **Lors d'un netcat :**
![alt text](https://github.com/Lucasmouchague/TP2-lucas_mouchague/blob/master/Trame%20NC.PNG)

## 5. Firewall
**Autorisation des pings**
`Panneau de configuration > Système et sécurité > Pare feu windows defender > Paramètre avancé > Nouvelle règle > Personnalisée > Protocole ICMPv4 > Toute adresse IP > autoriser la connexion.`
Sur le port 2000, l'envoi et la réception des messages marchent (partie Chat privé identique)

# 6. Manipulations d'autres outils/protocoles côté clients
**DHCP (sous windows)**
Dans une invite de commande : `ipconfig /all`
--> Serveur DHCP : 10.33.3.254
Bail expirant : lundi 7 janvier 2019 14:56:39

**Pour renouveler l'adresse IP**
Dans une invite de commande : `ipconfig /renew` (Impossible, merci Windows ??)

**DNS**
Dans une invite de commande : `ipconfig /all`
--> Serveur DNS: 10.33.10.20

Dans une invite de commande : `nslookup google.com`
--> Adresses :  2a00:1450:4007:810::200e 216.58.208.238
Ici on nous montre que pour le nom de domaine google.com il y a deux adresse IP (une IPV4 l'autre IPV6)

Dans une invite de commande : `nslookup ynov.com`
--> Adresse :  217.70.184.38
Ici on nous montre que pour le nom de domaine ynov.com il y a une adresse IP (IPV4)

Dans une invite de commande : `nslookup 78.78.21.21`
--> Nom : host-78-78-21-21.mobileonline.telia.com
Ici on nous montre le nom de domaine pour l'IP 78.78.21.21

Dans une invite de commande : `nslookup 92.16.54.88`
--> Nom : host-92-16-54-88.as13285.net
Ici on nous montre le nom de domaine pour l'IP 92.16.54.88
