
---
layout: post
title:  "Création d'un relais tor avec debian 10"
date:   2021-04-09 05:45:52 -0600
categories: Service
tags: Tor 
---
## Ajout du dépôt
On ajoute le dépôt de tor a nos source list, je prend jessie mais cela dépend de votre debian:
https://2019.www.torproject.org/docs/debian.html
Dans le fichier **/etc/apt/source.list** ajoutez ces lignes:
```
deb https://deb.torproject.org/torproject.org jessie main
deb-src https://deb.torproject.org/torproject.org jessie main
```

## Ajout de la clée GPG pour signer les packets
Vous aurez besoins de curl.
```
sudo apt install curl
```
Ensuite ajoutez la clés.
```
curl https://deb.torproject.org/torproject.org/A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89.asc | gpg --import
gpg --export A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89 | apt-key add -
```
## Installation de tor
```
apt update
apt install tor deb.torproject.org-keyring
```
## Configuration du relais
On vas copier le fichier de conf puis l’éditer.
```
sudo mv /etc/tor/torrc /etc/tor/torrc.default
sudo nano /etc/tor/torrc
```
J'y ai mis le minimum, je vous conseille de lire la doc si vous avez besoins de plus de paramètres.
```
##default bridge port 443
ORPort 1339
##uncomment if you don't want torproject.org to know your bridge
PublishServerDescriptor 0

Nickname [à changer] 
ContactInfo [à changer] 

##leave rest unchanged
log notice file /var/log/tor/tor.log
SocksPort 0
BridgeRelay 1
Exitpolicy reject *:*
```
**ORPort 1339**
C'est mon numéro de port, de base c'est 443.

**PublishServerDescriptor 0**
Pour ne pas montrer son bridge, je préfère ne pas le montrer car il ne sert qu'a une personne.

**log notice file /var/log/tor/tor.log**
Chemin du fichier de log.

Ensuite on verifie que la config est bonne.
```
sudo -u debian-tor tor --verify-config
```
## teste du relais 
On redémare tor.
```
sudo service tor restart
```
Puis on regarde les logs.
```
tail -f /var/log/syslog | grep tor -i
```
Si vous ne voyez pas de warning c'est que cela fonctionne, vous devriez voir des [notice]
 seulement.
 ## Ajout du relais 
 Ouvrez votre navigateur tor browser.
 Allez dans **option** puis **tor**.
 Cochez la case **utiliser un pont** puis **fournir un pont**
 Vous y entrer l'adresse IP du serveur ainsi que le port.
Pour ma part c'est 93.95.226.163:1339.
Puis redemarer le navigateur.
 
