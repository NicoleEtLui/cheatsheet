### INFRASTRUCTURE RESEAU - NETWORK INFRASTRUCTURE
#### Sommaire
* [Mode de configuration](#confMode)
  * [user](#userMode)
* [Identity](#identity)
* [Security](#security)
* [SSH](#ssh)
#### Configuration mode <a id="nomAncre"></a>
* `Router>` user EXEC mode  <a id="userMode"></a>
  * `enable` passer au mode de conf supérieur


* `Router#` privileged EXEC mode    
  * `configure terminal` passer au mode de conf supérieur
  * `no ip domain-lookup` désactive la résolution de nom, donc quand il ne connait pas une commande, il ne va pas tenter de résoudre cette commande comme si c'était une machine du réseau et tenter de la joindre.


* `Router(config)#` global configuration mode    
  * `interface ...` enter (sub-)interface configuration mode  
  * `router ...` enter routing configuration mode  


* `Router(config-line)#` line configuration mode
  * `login local` demande l'authentification par password lors d'une connection en local (directement sur la machine).
  * `transport input ssh` voir SSH.


#### Identity <a id="identity"></a>
* `hostname` nom de la machine dans le domaine

* `ip domain-name +nom-du-domaine` configure le nom du serveur dns, utiliser avec
`ip name-server +ip` qui identifie l'ip du serveur DNS.

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
