SERVEUR SOA - responsable de la zone -> recursion non autorisée !!!!

GLUE RECORD
- RR de type NS pour mondomaine.fr
- RR de type A pour mondomaine.fr

-- example.com -----------------------------------------------------------------
$ORIGIN  															; designates the start of this zone file in the namespace
$TTL 1h 																					; default expiration time of all resource records without their own
TTL value
example.com.		IN		SOA		ns.example.com. username.example.com. (
								2007120710												; serial number of this zone file
								1d																; slave refresh (1 day)
								2h																; slave retry time in case of a problem (2 hours)
								4w																; slave expiration time (4 weeks)
								1h																; maximum caching time in case of failed lookups (1 hour)
								)
example.com.		NS			ns												; ns.example.com is a nameserver for example.com
example.com.		NS 			ns.somewhere.example.			; ns.somewhere.example is a backup nameserver for example.com
example.com.		A 			192.0.2.1									; IPv4 address for example.com
								AAAA 		2001:db8:10::1 						; IPv6 address for example.com
ns							A 			192.0.2.2 								; IPv4 address for ns.example.com
								AAAA 		2001:db8:10::2 						; IPv6 address for ns.example.com
www 						CNAME 	example.com. 							; www.example.com is an alias for example.com
wwwtest 				CNAME 	www 											; wwwtest.example.com is another alias for www.example.com

-- wt20.ephec-ti.be ------------------------------------------------------------
$ORIGIN wt20.ephec-ti.be
$TTL    604800
@       IN      SOA     ns1.wt20.ephec-ti.be admin.wt20.ephec-ti.be (
                              3         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
; name servers - NS records
@        IN      NS      ns1.wt20.ephec-ti.be.
				 IN      MX      10 mail
; name servers - A records
ns1     IN      A       79.137.38.250

; services web

serverWeb       IN      A       151.80.119.124
www             IN      CNAME   serverWeb
b2b             IN      CNAME   serverWeb
intranet        IN      CNAME   serverWeb


/!\ dans le fichier de zone, l'ordre des choses semble importante ! 
ainsi le MX record doit être défini avec les autres RR qui utilisent @ ou autrement
dit le nom de domaine devant leur ligne de RR ... ( pas très clair) mais il faut ça
-> cd /

-> resolv.conf 
-> named.conf.options
-> fichier de zone 



Resolver DNS - responsable de résoudre les requêtes dns -> récursion obligatoire !

//QUANT A LA SEPARATION DU SOA ET DU RESOLVEUR !!!!!!!!!!!!!
-> en fait les requetes doivent passer par le resolveur pour arriver au SOA ...
=> dans resolv.conf, l'adresse doit être l'adresse ip du résolveur ! et pas celle 
du SOA 
=> dans le fichier de zone, le RR de type NS pointe bien vers le SOA

PROBLEME INTRANET

-> le fait de mettre deux nameserver dans resolv.conf ne semble pas fonctionner, 
chacun a leur tour ca fonctionne, mais les deux en même temps pas ...
-> solution apportée : 
mettre en place deux view dans le db.wt20.ephec-ti.be, une pour les ip trusted 
qui contient pour la zone wt20.ephec-ti.be, les RR des sites non-privé et qui 
redirige vers le SOA interne pour la zone intranet.ephec-ti.be 
la zone non trusted contient juste les rr des sites non privés
Il y aurait eu moyen d'utiliser qu'un serveur mais au moins si internet et 
intranet sont indépendants et donc si l'n-un crash l'autre reste, ...
-> vérifier intranet : 
> nslookup sur une machine externe = server can't find intranet.wt20.ephec-ti.be: NXDOMAIN
alors que sur une machine interne 

> nslookup intranet.wt20.ephec-ti.be
Server:         151.80.119.124
Address:        151.80.119.124#53

Non-authoritative answer:
intranet.wt20.ephec-ti.be       canonical name = serverWeb.wt20.ephec-ti.be.
Name:   serverWeb.wt20.ephec-ti.be
Address: 151.80.119.124

https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-14-04
https://www.centos.org/docs/5/html/Deployment_Guide-en-US/s1-bind-zone.html
https://hub.docker.com/r/resystit/bind9/
https://www.linux.com/learn/docker-volumes-and-networks-compose
https://www.linux.com/learn/introduction-docker-compose-tool-multi-container-applications
http://wiki.goldzoneweb.info/configuration_et_optimisations_de_bind
https://kb.isc.org/article/AA-00503/0/Whats-the-difference-between-allow-query-cache-and-allow-recursion.html
http://h41361.www4.hpe.com/docs/base_doc/DOCUMENTATION/V50_HTML/SUPPDOCS/BIND_GDE/OPTIONS.HTM#Directory
http://imrannazar.com/Intranet-DNS-Resolution-with-BIND-Views