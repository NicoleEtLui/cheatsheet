### INFRASTRUCTURE RESEAU - NETWORK INFRASTRUCTURE
#### Sommaire
* [Mode de configuration](#confMode)
  * [user](#userMode)
  * [privileged](#privilegedMode)
  * [config global](#globalMode)
  * [config line](#lineMode)
  * [config interface](#interfaceMode)
  * [config vlan](#confvlan)
  * [config routage](#routingMode)
* [Identity](#identity)
* [Security](#security)
* [SSH](#ssh)
* [VLAN](#vlan)
  * [Création](#creation)
  * [Configuration interfaces](#interfacevlan)
  * [Routage inter-Vlan](#intervlan)
    * [Router](#subif)
      * [IP vlan](#ipvlan_1)
      * [Access](#access_1)
      * [Trunk](#trunk_1)
    * [Switch Layer 3](#vlanL3)
      * [IP vlan](#ipvlan_2)
      * [Access](#access_2)
      * [Trunk](#trunk_2)
* [STP](#stp)
* [Routing](#routing)
  * [RIP](#RIP)
  * [OSPF](#OSPF)
  * [EIGRP](#EIGRP)
  * [Vieux matos](#old)
* [DHCP](#dhcp)
* [NAT](#nat)
  * statique
  * dynamique

#### MODE <a id="nomAncre"></a>
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
  * `ip helper-address` spécifie une destination à laquelle transmettre les broadcasts. Mécanisme par exemple utile lorsqu'on veut utiliser un serveur DHCP qui se trouve dans un autre réseau (et donc derrière un routeur).

<a id=confvlan></a>
* `Switch(config-vlan)#` vlan configuration mode
  * `name +nom` donne un nom au vlan

<a id="routingMode"></a>
* `Router(config-router)#` routing configuration mode.
  * `network +ip +wildcardMask` annonce un réseau connu en tant qu'information de routage.
  * `passive-interface +interface` permet d'empêcher l'envoi d'information de routage sur li'nterface précisée. Ce sont énormément d'informations sur le réseaux qui sont transmises il est donc bon de réduire au maximum la portée de ces transmissions la où ce n'est pas nécessaire.
  * `redistribute` permet de transmettre les infos concernant un protocole différents ou des routes statiques. Plus de précision dans [routing](#routing).

#### NETTOYAGE
* `erase start-up config`
* `delete vlan.dat` ou `delete flash:vlan.dat` ???

#### IDENTITY <a id="identity"></a>
* `hostname` nom de la machine dans le domaine
* `ip domain-name +nom-du-domaine` configure le nom du serveur dns, utiliser avec
`ip name-server +ip` configure l'ip du serveur DNS.
* `banner login #message#` Message qui apparaitra a la console si quelqu'un tente de se logger sur la machine, avant toute démarche de log. Il est possible d'utiliser n'importe quel caractère d'ouverture et de fermeture du message, ici '#'.
* `username admin privilege 15 secret cisco` enregistre un utilisateur *admin* de privilege *15* ( le plus haut ) dont le mot de passe est *secret*.

#### SECURITY  <a id="security"></a>
> certaines commandes de sécurité (notamment pour ssh) ne sont accessibles qu'en activant une license (achetée a cisco bien sure), activable avec la commande 
`license boot module +numModele technology-package securityk9`, après il faut `copy run start` et puis restrt la machine.

* `enable password +pswd` oblige le controle d'un mot de passe pour entrer en mode de configuration privilégié. [Possible de passer en option un mot de passe encrypté mais seulement de type 5 ou déja encrypté par du matos cisco.](http://www.cisco.com/c/en/us/td/docs/ios/12_2/security/command/reference/fsecur_r/srfpass.html)
* `enable secret +pswd` ajout une couche de sécurité en plus de `enable password` et annule le paswd configuré la possibilité d'utiliser le paswd de ce dernier !
* `service password-encryption` ecnrypte les password, ils ne seront plus visibles en clair dans des `sh run` par exemple.

###### [interfaces](http://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst6500/ios/12-2SX/configuration/guide/book/port_sec.pdf)

* `switchport port-security` active les modifications de sécurité ! Si pas tapée, rien n'agit
* `switchport port-security mac-adress +mac-adress` autorise une mac-adress sur cette interface
* `switchport port-security mac-adress sticky` autorise dynamiquement la mac-adress captée sur cette interface
* `switchport port-security max +num` définit le nombre max de mac-adress gardé en mémoire, si le nombre est atteint, plus aucune adresse ne peut être autorisé de quelque manière que ce soit
* `switchport port-security violation` définit le type de comportement en cas de violation

#### SSH    <a id="ssh"></a>
###### configuration globale
`ip ssh version 2` Pretty clear ...

`#crypto key generate rsa 2048 `
Génère une clé rsa de 2048 bit, le 2048 est a placé après !

###### configuration ligne vty
`transport input ssh` n'autorise la connexion à cette ligne que via ssh.

#### VLAN <a id=vlan></a>
#### Création <a id=creation></a>
* sur un switch (L2 ou L3), `vlan +num` entrer en mode de configuration de ce vlan ([voir configuration mode vlan](#confvlan)).

* il peut être intéressant de créer un vlan "poubelle", dans lequel on mettra toutes les interfaces n'appartenant à aucun vlan.
* il est bon de change le vlan natif (par défaut c'est le 1), mais ne pas désactiver le 1 car c'est par la que les premières attaques viendront. Et surtout c'est par le vlan 1 que STP passe.
* si l'interface de vlan est up-down, c'est qu'aucune interface réelle n'est incluse à ce vlan.
* Chez cisco, tous les vlans sont par défaut autorisés sur les trunks. Seulement chez cisco !!

#### Routage inter-vlan <a id=intervlan></a>
##### Sur un router <a id="subif"></a>
###### Ip vlan <a id="ipvlan_1"></a>
* Une sous interface par vlan, attention, l'encapsulation dot1q doit être faite avant pour qu'on puisse donner une ip a nos sous-interfaces
`encapsulation dot1q +numVlan +[native]` 
```
interface f0/0.20
    encapsulation dot1q 20 
    ip address 192.168.20.1 255.255.255.0
  ...
interface f0/0.30
    encapsulation dot1q 30
    ip address 192.168.30.1 255.255.255.0
  ...
```
###### mode access <a id="access_1"></a>
* sur un switch L2 ou L3
```
Switch(config)#interface F0/1/1
Switch(config-interface)#switchport mode access
Switch(config-interface)#switchport access vlan 20

```
###### mode Trunk <a id="trunk_1"></a>

```
Switch(config)#interface G0/2
Switch(config-interface)#switchport mode trunk
Switch(config-interface)#switchport trunk native vlan 50
```

##### Sur un layer 3 <a id="vlanL3"></a>
* il est important de désactiver la fonction de switching sur l'interface d'un L3 connecté à un autre routeur `(config-interface)#no switchport`.
###### Ip vlan <a id="ipvlan_2"></a>
* Comme un layer 3 possède plusieurs SVI (Switch Virtual Interface), on peut attribuer une ip par interface de VLAN.
```
interface vlan 20
  ...
  ip address 192.168.20.254 255.255.255.0
  ...
interface vlan 30
  ...
  ip address 192.168.30.254 255.255.255.0
  ...
```
###### Access <a id="access_2"></a>
* [Voir L2](#access_1)
###### Trunk <a id="trunk_2"></a>
* **Sur un L3**, il faut utiliser une ancienne commande pour configurer le trunk `switchport trunk encapsulation dot1q`, le reste est [pareil qu'avec un L2](#trunk_1)





#### ROUTING <a id="routing"></a>
###### RIP <a id="RIP"></a>
###### OSPF <a id="OSPF"></a>
* `router ospf 1` ici le numéro de process n'a pas d'importance, il n'a qu'une portée locale (contrairement à EIGRP ou il désignera le numéro d'AS, Système Autonome).
* Quand on déclare un réseau avec la commande ``network``, il est important de préciser l'area dans laquelle il se trouve. Ainsi pour des routeurs "interne" à une zone, on ne déclarera que des réseaux d'une même zone.
Par contre un **ABR** (Area Border Router), déclarera des réseaux de plusieurs zone.
ex :
```
R4(config-if)# router ospf 2
R4(config-router)# network 192.168.99.0 0.0.0.255 area 0
R4(config-router)# network 192.168.254.251 0.0.0.0 area 0
R4(config-router)# network 172.16.99.0 0.0.0.255 area 2
```


* `area +n°` paramètre de la zone 'n°'
  * `stub` en faire une zone 'stubby'
  * `nssa` en faire une zone 'not so stubby'
  * `range` résumé de route (ABR seulement)
* `default-information originate` redistribue les informations concernant la route par défaut seulement (seulement si il s'en trouve une dans la table de routage).
* `redistribute static subnets` permet de redistribuer de routes vers un un réseau (un ancien réseau par exemple, qui n'utiliserait pas OSPF mais relié à une zone OSPF) qu'elles soit classfull ou non.
* `redistribue connected subnets` ??? comprends pas la différence avec la précédente et surtout ...

* en OSPF il peut-être interessant de définir un ID (pour l'élection DR-BDR) au moyen d'une interface de loopback, `interface l0`.
* en mode privilégié, quelque commandes intéressantes :
  * `show ip route` table de routage
  * `show ip ospf neighbor` table de voisinage
  * ``

###### EIGRP <a id="EIGRP"></a>
###### Vieux matos <a id="old"></a>

#### DHCP <a id="dhcp"></a>
* `ip dhcp pool +nom`
  * `network 192.168.0.0 255.255.255.0`
  
#### NAT <a id="nat"></a> 

[NAT simplifiée](https://campus-virtuel.ephec.be/sites/2016_HE_T2032_9366_L2/_layouts/15/WopiFrame.aspx?sourcedoc=/sites/2016_HE_T2032_9366_L2/Documents/IRBP%20Semaine%2011/NAT%20version%20simplifi%C3%A9e/NAT_version%20du%207-12-2016.pdf&action=default)

###### statique

* en statique on associe une adresse ip interne privée à une adresse publique, utile par exemple quand on a besoin d'atteindre un serveur toujours à la même adresse. 
Il faut préciser sur le routeur NAT quel interface est en inside et quelle interface est en outside et puis déclarer la NAT proprement dite

```
interface GigabitEthernet0/0
...
ip nat inside 

interface GigabitEthernet0/1
...
ip nat outside 
...
ip nat inside source static 10.0.0.2 202.0.5.1
```
###### dynamique
