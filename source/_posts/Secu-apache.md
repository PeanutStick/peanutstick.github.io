---
layout: post
title:  "Securing an apache server"
date:   2021-03-09 05:45:52 -0600
categories: Security
---

## Disable indexing


/etc/apache2/apache2.conf

It must be activated on all \<Directory /path\> except at the root 
>\<Directory /var/www/\>
>Options Indexes FollowSymLinks
>AllowOverride None
>Require all granted
>\</Directory\>

To deactivate the directory listing, edit the second line:

>\<Directory /var/www/\>
>Options FollowSymLinks
>AllowOverride None
>Require all granted
>\</Directory\>

## Restrict access to the configuration files


It's possible to download the files even if the indexing is disabled


To block txt files

>\<Files \~ \"\\.txt\$\"\>
>Order allow,deny
>Deny from all
>\</Files\>

## Hide version of Apache

We can see the apache version easily in the network tab while you use the inspector

/etc/apache2/apache2.conf 
Add these lines at the bottom :

>ServerTokens Prod
>ServerSignature Off

## Disable ETags


ETag is a server response header that allows browsers to make conditional requests and perform cache validation efficiently. On the other hand, it poses security risks in case it gets leaked by your code, and can result in cache poisoning attack on your website. So it is better to disable ETag if your website does not need it.
[https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/ETag](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/ETag)

/etc/apache2/apache2.conf
Add this line at the bottom :

>FileETag None

## Removal of guest rights


>cd /etc/apache2
>chmod 750 \* -R

7 = All rights for the root user

5 = Read and execute for the group

0 = Nothing for guest

## Securing against http Verb Tampering

HTTP Verb Tampering is an attack that exploits vulnerabilities in HTTP verbauthentication and access control mechanisms.

1.  Upload the file image.php.png

2.  The file valide because of png

3.  Pausing the request

4.  Rename the file in image.php

5.  The file image.php is on the server

/etc/apache2/apache2.conf

Add Deny from all like this but don't us it in the root:

>\<LimitExcept POST GET\>
>Deny from all
>\</LimitExcept\>

[https://stackoverflow.com/questions/40914743/apache-limitexcept-only-to-get-and-post-methods](https://stackoverflow.com/questions/40914743/apache-limitexcept-only-to-get-and-post-methods)

## Xss protection


Enable this module

>a2enmod headers

/etc/apache2/apache2.conf

Add the like like this:

>\<Directory /\>
>Header set X-XSS-Protection \"1; mode=block\"
>\</Directory\>

## Restriction du fichier .htaccess

On empêche la lecture du fichier htaccess par un utilisateur
```
\<Directory /\>

AllowOverride None

\</Directory\>
```
## Protection contre le vol de cookie


**Ne Pas le faire pour iTop, il demande une connexion a chaque page car
il garde la connexion grâce au cookie et pas avec une variable de
session !!!**

Puis dans le fichier /etc/apache2/apache2.conf on ajoute.
```
\<Directory /\>

Header edit Set-Cookie \^(.\*)\$ \$1;HttpOnly;Secure

\</Directory\>
```
## Protection contre le clickJacking


Dans le fichier /etc/apache2/apache2.conf on ajoute
```
\<Directory /\>

Header always append X-Frame-Options SAMEORIGIN

\</Directory\>
```
![](media/image4.png)

## Limiter les DOS


Dans le fichier /etc/apache2/apache2.conf

On passe le timeout de 300 à 10.
```
MaxClients 150

KeepAlive On

MaxKeepAliveRequests 100

KeepAliveTimeout 10
```
Il ne sera pas totalement protégé mais il sera moins vulnérable.

## Cacher le manuel apache2


Le manuel permet d'avoir la version d'apache2

nano /etc/apache2/conf-available/apache2-doc.conf

Commentez la première ligne

![](media/image5.png)

## Protection contre le reniflage MIME


[https://www.keycdn.com/support/what-is-mime-sniffing]](https://www.keycdn.com/support/what-is-mime-sniffing)

nano /etc/apache2/conf-available/security.conf

Ajoutez ces lignes
```
\<IfModule mod\_headers.c\>

Header always set X-Content-Type-Options \"nosniff\"

\</IfModule\>
```
## Restreindre l'accès à /icons/README


nano /etc/apache2/apache2.conf
```
\<Directory \"/usr/share/apache2/icons\"\>

Options FollowSymlinks

AllowOverride None

Order allow,deny

Deny from all

\</Directory\>
```
## Mise en place de Modsecurity


### Installation

Modsecurity est un pare-feu applicatif, dont le rôle est de filtrer les
requêtes entrant sur un serveur HTTP Apache. Il se présente sous la
forme d\'un module apache, qui analyse les requêtes reçues grâce à
l\'emploi d\'une base des règles de requêtes considérées comme non
souhaitées.

[https://www.inmotionhosting.com/support/server/apache/install-modsecurity-apache-module/](https://www.inmotionhosting.com/support/server/apache/install-modsecurity-apache-module/)

sudo apt update -y

sudo apt install libapache2-mod-security2

sudo systemctl restart apache2

Vérification que le paquet a une version supérieure à 2.9

apt-cache show libapache2-mod-security2 \| grep -E
\'(Version\|Package)\'

![](media/image6.png)

sudo cp /etc/modsecurity/modsecurity.conf-recommended
/etc/modsecurity/modsecurity.conf

sudo nano /etc/modsecurity/modsecurity.conf

Changer SecRuleEngine DetectionOnly pour SecRuleEngine On

![](media/image7.png)

### Configuration

On télécharge la config d'OWASP, un organisme reconnu dans le monde de
la sécu.

Cd /tmp

wget https://github.com/coreruleset/coreruleset/archive/v3.3.0.zip

unzip FileName.zip

mv coreruleset-3.3.0/crs-setup.conf.example
/etc/modsecurity/crs-setup.conf

mv coreruleset-3.3.0/rules/ /etc/modsecurity/

nano /etc/apache2/mods-enabled/security2.conf

Ajoutez ça et commentez commentez la dernière ligne.
```
IncludeOptional /etc/modsecurity/\*.conf

Include /etc/modsecurity/rules/\*.conf
```
![](media/image8.png)

systemctl restart apache2

## Logs

On peut observer en direct les logs avec

tail -f /var/log/apache2/error.log (liste les attaques et warning)

tail -f /var/log/apache2/modsec\_audit.log (liste tout)

tail -f /var/log/apache2/access.log (logs clairs avec IP re ressource
chargée)

## Fail2ban

Scans
-----

### Nikto

Nikto -host 192.168.101.147 -o scan\_iTop.html

![](media/image9.png)

### Dirb

Dirb http://192.168.101.147

Pour l'aide :

dirb

Il est possible de changer de word list :

dirb http://url/ /usr/share/dirb/wordlists/vulns/apache.txt

![](media/image10.png)
