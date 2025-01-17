We're going to create, and configure, a virtual network (VN) in Azure, then we'll add two virtual machines to see if they're communicating well between each other

## Goals :
- Create a VN in Azure.
- Add two VM and test the communication.

## First step : Create and configure the virtual network
Firstly, we're going to create a virtual network, then configure its settings, by going into the research bar of Azure and researching "Virtual Network". 

![Recherche VN](img/vn_search.png)

Then, click on "Create".

![Creation VN](img/vn_create.png)

You can also see, in the page, all the virtual network created in the same Azure subscription as yours.

### Base informations
[//]: <> (Check the multiple names, they're basic translation of what was written in Azure.)
After you've pressed the button "Create", you'll arrived in the first section of the creation of a virtual network, called : "Base informations".
Here, it is important to precise, in "Project details", your subscription (which normally add itself automatically) and your ressource group, so that Azure doesn't create a new group (in my case, my ressource groupe is called : "pr38ehf").

The name of the virtual netowrk, in "Instance details", can be anything you like (in my case, I've chosen : "VN-GabinChautemps"), and like your subscription, the region will add itself automatically.

![Détails de l'instance](img/vn_details.png)

[//]: <> (Reworte the first half omg :/)
After "filling the gaps", click on "Next".

### Sécurité
In the security section, it is possible to configure multiple security functionality, like a firewall, directly into the virtual newtowrk. However, for simplicity, we won't do it here. So click, again, on "Next".

### Adresses IP
Now, we'll configure the range of the private IP addresses in our virtual netowrk.

Une adresse IP privée est une adresse qui est pour usage sur un réseau interne (par exemple : réseau d'entreprise, réseau à la maison) attribué par le protocole DHCP (Dynamic Host Configuration Protocol). Elle permet aux appareils, connectés sur le même réseau, de communiquer entre eux de manière sécurisé et efficace. Cependant, il n'est pas possible de "surfer" sur Internet avec une adresse IP privée.

Il existe trois classe d'adresses IP privée :
- Classe A :
	- De 10.0.0.0 à 10.255.255.255
- Classe B :
	- De 172.16.0.0 à 172.31.255.255
- Classe C :
	- De 192.168.0.0 à 192.168.255.255

Dans cette documentation, je vais utiliser la classe C pour l'adressage, mais la manipulation devrait être la même pour les deux autres classes.

Pour commencer, définissez votre plage d'adresse IP privée en CIDR 23 (/23). Le CIDR permet de créer des "super réseaux" avec plus de flexibilité, comparé aux autres méthodes d'adressage.

![Définition d'une plage d'adresse](img/ip_config.png)

Attention, vérifiez que votre réseau ne se superpose pas avec un autre réseau virtuel, sinon votre réseau ne fonctionnera pas.

Ensuite, ajoutez un sous-réseau en cliquant sur "Ajouter un sous-réseau". Cela ouvrira une nouvelle page.

Puis, choisissiez le nom de votre sous-réseau (dans mon cas, j'ai choisi : Chautemps-Front). 

![Définition du nom du SR](img/subnet_name.png)

L'objectif du sous-réseau peut rester en "Default".

Après cela, définissez la plage du sous-réseau en CIDR 24 (donc /24).

![Définition plage du SR](img/subnet_range.png)

Si vous avez un groupe de sécurité réseau (NSG), ajoutez-le, dans la section sécurité (personnellement, mon groupe de sécurité s'appelle : "SG-Gabin"). 

![Ajout du NSG](img/nsg_added.png)

L'avantage de définir votre groupe de sécurité réseau sur votre sous-réseau est de pouvoir filtrer le trafic, de votre réseau virtuel, avec les règles de votre groupe de sécurité réseau. Comme avec un pare-feu (firewall). Donc, cela rajoute une couche de sécurité en plus. De cette manière, en définissant des règles précises, vous protégez vos machines virtuelles des attaques extérieures et vous empêchez les accès non autorisés.

Après avoir ajouté votre groupe de sécurité réseau (ou pas), cliquez sur le bouton "Ajouter" tout en bas de la page. Vous pourrez ensuite voir dans la section où vous avez configuré la plage d'adresse IP privée, votre sous-réseau.

![Section plage SR](img/show_range.png)

Pour finir, cliquez sur "Vérifier + créer", puis sur "Créer".

### Définir d'autres DNS dans le réseau virtuel
Cette étape est totalement optionnel, mais je vais quand même l'expliquer dans cette documentation.

Il est possible de configurer un DNS, au lieu d'utiliser ceux directement fournit par Azure, dans les paramètres de votre réseau virtuel. Pour effectuer cela, recherchez, dans la barre de recherche "Réseau virtuels", puis dans la liste ci-dessous, recherchez votre réseau virtuel. Ensuite dans la barre de rechercher, qui se situe à gauche de la page, recherchez "Serveurs DNS", et choisissiez "Personnalisé".

Il existe plusieurs serveurs DNS gratuits, dans mon cas, j'ai choisi les IP des serveurs de Cloudflare (1.1.1.1 et 1.0.0.1).

![Serveur DNS](img/dns.png)

Cliquez sur "Enregistrer" pour sauvegarder vos modifications.

## Deuxième étape : Ajout de machines virtuelles dans le réseau virtuel
Pour ajouter des machines virtuelles dans votre réseau virtuel, vous devrez passer par l'interface réseau de celle-ci :

![L'interface réseau d'une machine](img/ext_network.png)

Ces options se mettront automatiquement lorsque vous créez une nouvelle machine virtuelle. Cependant, il n'est pas possible d'ajouter des machines virtuelles déjà créées dans votre réseau virtuel.

## Troisième étape : Connexion SSH aux machines virtuelles
Pour la connexion en SSH, voici comme cela va se dérouler :

![Schéma connexion SSH vers mes machines](img/schéma_projet_réseau_virtuel(1).jpg)

D'abord, je lancerais deux instances Linux en utilisant WSL2 (j'utiliserais Ubuntu pour me connecter sur ma première machine, puis Kali Linux pour me connecter sur ma deuxième machine).

![Mes machines en utilisant WSL](img/wsl_machines.png)

Ensuite, j'ai effectué les mêmes étapes ci-dessous deux fois pour me connecter aux machines virtuels sur Azure :
- Déplacer la clé dans le dossier "~/.ssh/".
- Changer les accès de la clé en "READ ONLY".
- Connexion sur la machine en utilisant SSH, avec l'adresse IP de la machine virtuel.

[//]: <> (A ajouter le lien vers l'autre doc)
(i) voir doc labo flask pour plus d'info

![Connexion en SSH sur mes deux machines](img/steps_to_ssh.png)
La différence entre une adresse IP publique est une adresse IP privé est que les adresse IP publique sont utilisées pour interagir avec Internet, elles ne peuvent pas être utilisé dans un réseau local privé, comparé aux adresse IP privée. Et voici les plages disponibles des adresses IP publique :
- De 1.0.0.0 à 9.255.255.255
- De 11.0.0.0 à 126.255.255.255
- De 129.0.0.0 à 169.253.255.255
- De 169.255.0.0 à 172.15.255.255
- De 172.32.0.0 à 191.0.1.255
- De 192.0.3.0 à 192.88.98.255
- De 192.88.100.0 à 192.167.255.255
- De 192.169.0.0 à 198.17.255.255
- De 198.20.0.0 à 223.255.255.255

## Quatrième étape : Test de communication
Pour vérifier si les deux machines, dans notre réseau virtuel, peuvent bien communiquer, on peut effectuer la commande "ping".

Tout d'abord, trouvez les adresses IP privé des deux machines virtuelles avec la commande suivante, dans votre terminal :

```
ip a
```

[//]: <> (A voir s'il faut masquer une info ici, j'ai pas envie que les gens me dox :p)
[//]: <> (Je crois qu'il y a pas grand chose mais je veux juste être sûr.)
![Commande "ip a" effectué dans le terminal](img/command_ip_a.png)

On peut voir que ma machine virtuelle n°1 (celle du haut) à l'adresse IP "192.168.2.4/24", et la machine n°2 (celle du bas) à l'adresse IP "192.168.2.5/24".

Donc, maintenant que nous avons les adresses IP des deux machines virtuelles, on va finalement pouvoir les "pinger" entre eux. C'est-à-dire, votre machine n°1 va effectuer un ping sur l'adresse IP de votre machine n°2, dans mon cas la commande est la suivante :

```
ping 192.168.2.5
```

Et votre machine n°2 va effectuer un ping sur l'adresse IP de votre machine n°1, voici encore une fois la commande, dans mon cas :

```
ping 192.168.2.4
```

![Les deux commandes ping effectué dans le terminal](img/pung.png)

Vous devrez normalement voir chaques paquets arrivée à leurs destinations, avec le temps que cela a pris. Si c'est le cas, c'est que les machines peuvent bien communiquer entre elles, et donc c'est réussi !

Pour arrêter les pings, vous pouvez effectuer la combinaison de touche "CTRL+C" dans votre terminal.
