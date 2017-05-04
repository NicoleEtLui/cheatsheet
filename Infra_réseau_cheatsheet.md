### INFRASTRUCTURE RESEAU - NETWORK INFRASTRUCTURE
#### Sommaire
* [Mode de configuration](#confMode)
  * [user](#userMode)
  * [privileged](#privilegedMode)
  * [config global](#globalMode)
  * [config line](#lineMode)
  * [config interface](#interfaceMode)
  * [config routage](#routingMode)
* [Identity](#identity)
* [Security](#security)
* [SSH](#ssh)
* [Routing](#routing)
  * [OSPF](#OSPF)
  * [EIGRP](#EIGRP)

#### Configuration mode <a id="nomAncre"></a>
<a id="userMode"></a>
* `Router>` user EXEC mode  
  * `enable` passer au mode de conf supérieur

<a id="privilegedMode"></a>
* `Router#` privileged EXEC mode    
  * `configure terminal` passer au mode de conf supérieur
  * `no ip domain-lookup` désactive la résolution de nom, donc quand il ne connait pas une commande, il ne va pas tenter de résoudre cette commande comme si c'était une machine du réseau et tenter de la joindre.
  * `show protocols` information de couche 2 et 3 comme `show ip interface brief` mais avec la présence du subnet mask en plus.
  * `show ip protocols` montre des informations sur les différents protocoles de routage configurés
<a id="globalMode"></a>
* `Router(config)#` global configuration mode    
  * `interface ...` enter (sub-)interface configuration mode  
  * `router ...` enter routing configuration mode  
  * `line ...` enter line mode configuration (console or vty)

<a id="lineMode"></a>
* `Router(config-line)#` line configuration mode
  * `login local` demande l'authentification par password lors d'une connection en local (directement sur la machine).
  * `logging synchronous` synchronise les logs de cette ligne avec le terminal de la machine. Il est possible de régler le niveau de level des logs que l'on veut afficher
  * `transport input ssh` voir [SSH](#ssh).

<a id="interfaceMode"></a>
* `Router(config-interface)#` interface configuration mode
  * `description +msg` Pas de règles quant à la forme du msg, pas besoin de délimiteur.
  * `ip address +ip +mask` adresse de l'interface
  * `clock rate` définit le clock rate **seulement nécessaire sur une liaison série !**
  * `shutdown` désactive une interface, souvent utilisée dans sa version négative `no shutdown` pour activer l'interface.

<a id="routingMode"></a>
* `Router(config-router)#` routing configuration mode.
  * `network +ip +wildcardMask` annonce un réseau connu en tant qu'information de routage.
  * `passive-interface +interface` permet d'empêcher l'envoi d'information de routage sur li'nterface précisée. Ce sont énormément d'informations sur le réseaux qui sont transmises il est donc bon de réduire au maximum la portée de ces transmissions la où ce n'est pas nécessaire.
  * `redistribute` permet de transmettre les infos concernant un protocole différents ou des routes statiques. Plus de précision dans [routing](#routing).


#### Identity <a id="identity"></a>
* `hostname` nom de la machine dans le domaine
* `ip domain-name +nom-du-domaine` configure le nom du serveur dns, utiliser avec
`ip name-server +ip` configure l'ip du serveur DNS.
* `banner login #message#` Message qui apparaitra a la console si quelqu'un tente de se logger sur la machine, avant toute démarche de log. Il est possible d'utiliser n'importe quel caractère d'ouverture et de fermeture du message, ici '#'.
* `username admin privilege 15 secret cisco` enregistre un utilisateur *admin* de privilege *15* ( le plus haut ) dont le mot de passe est *secret*.

#### Security  <a id="security"></a>
* `enable password +pswd` oblige le controle d'un mot de passe pour entrer en mode de configuration privilégié. [Possible de passer en option un mot de passe encrypté mais seulement de type 5 ou déja encrypté par du matos cisco.](http://www.cisco.com/c/en/us/td/docs/ios/12_2/security/command/reference/fsecur_r/srfpass.html)
* `enable secret +pswd` ajout une couche de sécurité en plus de `enable password` et annule le paswd configuré la possibilité d'utiliser le paswd de ce dernier !
* `service password-encryption` ecnrypte les password, ils ne seront plus visibles en clair dans des `sh run` par exemple.

#### SSH    <a id="ssh"></a>
###### configuration globale
`#crypto key generate rsa 2048 `
Génère une clé rsa de 2048 bit, le 2048 est a placé après !

###### configuration ligne vty
`transport input ssh` n'autorise la connexion à cette ligne que via ssh.

#### Routing <a id="routing"></a>
###### OSPF <a id="OSPF"></a>
* `router ospf 1` ici le numéro de process n'a pas d'importance, il n'a qu'une portée locale (contrairement à EIGRP ou il désignera le numéro d'AS, Système Autonome).
* Quand on déclare un réseau avec la commande ``network``, il est important de préciser l'area dans laquelle il se trouve. Ainsi pour des routeurs "interne" à une zone, on ne déclarera que des réseaux d'une même zone.
Par contre un **ABR** (Area Border Router), déclarera des réseaux de plusieurs zone.
ex :
```
R4(config-if)# router ospf 2
R4(config-router)# network 192.168.99.0 0.0.0.255 area 0
R4(config-router)# network 192.168.254.251 0.0.0.0 area 0
R4(config-router)# network 172.16.99.0 0.0.0.255 area 2 ```

* `area +n°` paramètre de la zone 'n°'
  * `stub` en faire une zone 'stubby'
  * `nssa` en faire une zone 'not so stubby'
  * `range` résumé de route (ABR seulement)
* `default-information originate` redistribue les informations concernant la route par défaut seulement (seulement si il s'en trouve une dans la table de routage).
* `redistribute static subnets` permet de redistribuer de routes vers un un réseau (un ancien réseau par exemple, qui n'utiliserait pas OSPF mais relié à une zone OSPF) qu'elles soit classfull ou non.

* en OSPF il peut-être interessant de définir un ID (pour l'élection DR-BDR) au moyen d'une interface de loopback, `interface l0`.
* en mode privilégié, quelque commandes intéressantes :
  * `show ip route` table de routage
  * `show ip ospf neighbor` table de voisinage
  * ``

###### EIGRP <a id="EIGRP"></a>
