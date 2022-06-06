# PROJET: DOCKER (Parcours Cyber)

## Objectifs et présentation du projet 
### Objectifs :
  - Utiliser des données déjà existantes afin de les travailler sur le Docker. Ainsi, on ne retravaille pas toute la pipeline. 
  - Passer par un orchestrateur (Docker Compose) afin de simplifier la communication entre les conteneurs

### Présentation Projet/Cyber
Pour le parcours Cybersécurité, le projet consiste à créer les trois conteneurs suivants:
  - un conteneur qui tourne un service web 
  -	un pare-feu
  -	un client
  
Ces trois conteneurs seront tous liés entre eux. En effet, pour qu’un client puisse accéder au service web, il faudra que celui-ci passe par le pare-feu

-----------------
## Solution proposée

- Dockerfile Server
```

```
- Dockerfile Client
```

```

- Dockerfile Firewall

```

```
- Docker-Compose

```

```

Afin de réaliser ce projet, j’ai suivi les étapes suivantes :
- I.	Création des 3 Dockerfile: Serveur Web / Client / Pare-feu
- II.	Réglages réseau et adressage IP
- III.	Test de la maquette
- IV.	Docker Compose




## I.	Création des 3 Dockerfile: Serveur Web / Client / Pare-feu




J’ai commencé par créer et configurer séparément les Dockerfile de trois conteneurs, tous de type debian: un conteneur qui tournera un serveur Web (Apache2), un conteneur qui fera office de Client et enfin un dernier qui jouera le rôle de pare-feu et limitera donc la communication entre ces containers selon certaines conditions. 

On commence par faire « sudo apt-get update » dans notre machine virtuelle debian .
Notre machine hôte a l’adresse IP suivante :
 ![image](https://user-images.githubusercontent.com/56343178/172078502-afa48370-9f29-4391-bd3a-04a7afe770d9.png)



### 1.	Configuration Serveur Web (Apache2)

Pour le serveur Web , nous utiliserons le service Web Apache2. 
On commence par créer notre répertoire « server » dans notre machine virtuelle Docker. Puis on y ajoute le Dockerfile suivant permettant d’afficher un premier site web, qui correspond ici à mon CV réalisé dans le module « Développement d’applications Web » de ce semestre et qui aura été importé à partir de mon github :
  
 ![image](https://user-images.githubusercontent.com/56343178/172078559-53a0b4ac-fc20-4488-b3ef-47107ffcb314.png)

 ![image](https://user-images.githubusercontent.com/56343178/172078565-d8851af9-2352-42e8-979e-7ae7c37523a9.png)

 

On enregistre le fichier puis on lance la commande suivante : « docker build -t server . »
On obtient le résultat suivant :
 
 

On vérifie maintenant que l’image a bien été créée en lisant les images existantes avec la commande « sudo docker images » :
 
L'image étant créée, on instancie maintenant un conteneur à partir de celle-ci en exposant le port 8080 vers le port 80 et le lançant en arrière-plan :
 
On vérifie que le conteneur est bien en cours d’exécution avec la commande « sudo docker ps » :
 

On ouvre maintenant un navigateur et on indique l'adresse IP de notre machine hôte Docker (172.17.0.1) ou localhost, en spécifiant le port "8080" que nous avons choisit précédemment. On a le résultat suivant :

 

2.	Configuration Client

On commence par créer notre répertoire « client » dans notre machine virtuelle Docker , puis on y ajoute le Dockerfile suivant qui permet de télécharger les outils nécessaires au Client pour communiquer avec un le Serveur et le Firewall et pouvoir  accéder au site Web :
 
On peut voir que l’image « client » a bien été crée :
  
 
 




Nous créons ensuite un container client1 utilisant cette nouvelle image « client » : 
 

On vérifie maintenant que le Client1 a bien accès au site web avec la commande : « curl http://172.17.0.1:8080 »
	Client 1 :
 
3.	Configuration Pare-feu

On passe maintenant à la configuration du Pare-feu. Pour cela tout comme pour le serveur, on crée une nouvelle image. On crée un dossier « firewall » et on y ajoute le Dockerfile suivant qui installe d’une part le paquet « iptables » puis copie le script fw.sh (contenant les différentes règles à appliquer) dans le répertoire du container:
 
 
 
Fichier « fw.sh » :
 

On construit ensuite l’image firewall :
 
 
On peu voir que l’image firewall a bien été crée :
 
On crée maintenant une instance de cette image, le container « firewall » et on lance un terminal en mode privilégié :
 
 
Puis on vérifie que la règle a bien été ajoutée :
 


II. Réglages réseau et adressage IP

On s’occupe ensuite du réglage réseau et l’adressage ip :
-	le Server appartiendra au réseau par défaut « bridge » d’adresse 172.17.0.0/24 et a pour adresse IP 172.17.0.3/24
 
-	le Client1 appartiendra au réseau « network1 »  d’adresse 172.18.0.0/24 et a pour adresse IP 172.18.0.3/24
Ainsi, on modifie l’adresse IP du Client 1 pour qu’il appartienne au réseau 172.18.0.0/24 :
 
On déconnecte notre Client1 de son réseau par défaut bridge :
 
Puis on ajoute notre Client1 à notre nouveau réseau network1:
 
On peut voir que le Client1 a bien reçu une adresse IP appartenant au réseau défini précédemment : 172.18.0.3
 
-	Le FireWall appartiendra au deux réseaux 172.17.0.0/24 et 172.18.0.0//24 et a donc les deux adresses IP suivantes : 172.0.17.2/24 et 172.0.18.2/24


III.	Test de la maquette

On vérifie d’abord que le Client1 a bien accès au serveur Web avant l’activation du pare-feu :
	Client1 :
 
Pour tester le Pare-feu, on teste donc la règle définie précédemment dans le fichier fw.sh qui permet d’interdire l’accès au site au Client1 en activant le pare-feu puis en réessayant de faire un curl avec le Client 1. 
Cependant, je n’ai pas réussi à appliquer cette règle sur le Client1 car je n’arrive pas à définir les deux  adresses IP du Firewall comme passerelle par défaut des containers Server et Client1 pour qu’il agisse comme un routeur. Donc j’ai testé la commande « curl » directement à partir du Firewall :
 
On active maintenant le pare feu en exécutant le fichier fw.sh contenant la règle puis on refait un curl :
 
On voit bien que l’accès est maintenant bloqué.









IV.	Docker Compose
On commence par installer Docker Compose avec la commande :  
« sudo curl -L https://github.com/docker/compose/releases/download/v2.0.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose »
 
On modifie les droits en exécution avec la commande :  « sudo chmod +x /usr/local/bin/docker-compose »
 Puis on vérifie l’installation avec la commande « docker-compose –version »
 
Puis on fait « docker-compose build »:
 
Et enfin « docker-compose up » :
 
