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



-----------------
# I.	Création des 3 Dockerfile: Serveur Web / Client / Pare-feu

J’ai commencé par créer et configurer séparément les Dockerfile de trois conteneurs, tous de type debian: un conteneur qui tournera un serveur Web (Apache2), un conteneur qui fera office de Client et enfin un dernier qui jouera le rôle de pare-feu et limitera donc la communication entre ces containers selon certaines conditions. 

On commence par faire « sudo apt-get update » dans notre machine virtuelle debian .
Notre machine hôte a l’adresse IP suivante :
 ![image](https://user-images.githubusercontent.com/56343178/172078502-afa48370-9f29-4391-bd3a-04a7afe770d9.png)



## 1.	Configuration Serveur Web (Apache2)

Pour le serveur Web , nous utiliserons le service Web Apache2. 
On commence par créer notre répertoire « server » dans notre machine virtuelle Docker. Puis on y ajoute le Dockerfile serveur montré précédemment permettant d’afficher un premier site web, qui correspond ici à mon CV réalisé dans le module « Développement d’applications Web » de ce semestre et qui aura été importé à partir de mon github :
  
 ![image](https://user-images.githubusercontent.com/56343178/172078559-53a0b4ac-fc20-4488-b3ef-47107ffcb314.png)

 ![image](https://user-images.githubusercontent.com/56343178/172078565-d8851af9-2352-42e8-979e-7ae7c37523a9.png)


On enregistre le fichier puis on lance la commande suivante : « docker build -t server . »
On obtient le résultat suivant :
 ![image](https://user-images.githubusercontent.com/56343178/172078838-26238325-5f39-4226-ac39-a7ac762b5055.png)

 ![image](https://user-images.githubusercontent.com/56343178/172078979-d007646f-e4db-4c0d-aca0-93fc10dfaa27.png)


On vérifie maintenant que l’image a bien été créée en lisant les images existantes avec la commande « sudo docker images » :
 ![image](https://user-images.githubusercontent.com/56343178/172078854-ff9c3cbf-4f59-44db-afcd-ebfc2ec1c71a.png)

L'image étant créée, on instancie maintenant un conteneur à partir de celle-ci en exposant le port 8080 vers le port 80 et le lançant en arrière-plan :
 ![image](https://user-images.githubusercontent.com/56343178/172078856-44ee2ea3-bb2f-4c5b-a682-34351772deac.png)

On vérifie que le conteneur est bien en cours d’exécution avec la commande « sudo docker ps » :
 ![image](https://user-images.githubusercontent.com/56343178/172078863-03bad3dd-91b7-473d-9ca4-e2e1bcf9a1f2.png)


On ouvre maintenant un navigateur et on indique l'adresse IP de notre machine hôte Docker (172.17.0.1) ou localhost, en spécifiant le port "8080" que nous avons choisit précédemment. On a le résultat suivant :

 ![image](https://user-images.githubusercontent.com/56343178/172078872-a7f14bc0-4739-4cb4-9557-51b2ec98c7af.png)

-----------------
## 2.	Configuration Client

On commence par créer notre répertoire « client » dans notre machine virtuelle Docker , puis on y ajoute le Dockerfile Client vu précédemment qui permet de télécharger les outils nécessaires au Client pour communiquer avec  le Serveur et le Firewall et pouvoir  accéder au site Web.
On peut voir que l’image « client » a bien été crée :
  ![image](https://user-images.githubusercontent.com/56343178/172078949-252cc147-9a07-4566-a4cb-714b1c0c6de4.png)
 
 ![image](https://user-images.githubusercontent.com/56343178/172079017-d575c3c4-f9ca-495b-a931-95da0e4f8e8d.png)
![image](https://user-images.githubusercontent.com/56343178/172079022-bcdccc1c-5371-4364-8c36-408746f76639.png)


Nous créons ensuite un container client utilisant cette nouvelle image « client » : 
![image](https://user-images.githubusercontent.com/56343178/172080436-2e581528-486c-4aad-b88a-bf5181c76854.png)
 

On vérifie maintenant que client a bien accès au site web avec la commande : « curl http://172.17.0.1:8080 »
	Client :
 ![image](https://user-images.githubusercontent.com/56343178/172079067-ed0e0da3-6803-4087-b825-bcb7d8426cfd.png)

-----------------
## 3.	Configuration Pare-feu

On passe maintenant à la configuration du Pare-feu. Pour cela tout comme pour le serveur, on crée une nouvelle image. On crée un dossier « firewall » et on y ajoute le Dockerfile Firewall vu précédemment qui installe d’une part le paquet « iptables » puis copie le script fw.sh (contenant les différentes règles à appliquer) dans le répertoire du container:
 
 ![image](https://user-images.githubusercontent.com/56343178/172079091-6b13c00c-699b-4401-aa1a-e1496fa7ab60.png)

 
### Fichier « fw.sh » :
```
```

On construit ensuite l’image firewall :
 ![image](https://user-images.githubusercontent.com/56343178/172079195-45b694ab-f857-4a4f-b2ee-1bfa0917102b.png)
![image](https://user-images.githubusercontent.com/56343178/172079198-9fcab764-97d3-4164-834c-d5d336b7e31c.png)

 
On peut voir que l’image firewall a bien été crée :
 ![image](https://user-images.githubusercontent.com/56343178/172079208-2fcef096-e169-46b3-ad2b-4b92497018f0.png)

On crée maintenant une instance de cette image, le container « firewall » et on lance un terminal en mode privilégié :
 ![image](https://user-images.githubusercontent.com/56343178/172079216-431024a2-53e7-4d14-bf40-70916e7a4a53.png)
![image](https://user-images.githubusercontent.com/56343178/172079225-7436b321-a78a-4b69-9f79-69b15df9cac2.png)

 
Puis on vérifie que la règle a bien été ajoutée :
 
![image](https://user-images.githubusercontent.com/56343178/172079228-b4c897b4-b4e0-4f7e-a8e0-c930a7cc95d9.png)


# II. Réglages réseau et adressage IP

On s’occupe ensuite du réglage réseau et l’adressage ip :
-	le Server appartiendra au réseau par défaut « bridge » d’adresse 172.17.0.0/24 et a pour adresse IP 172.17.0.3/24
 ![image](https://user-images.githubusercontent.com/56343178/172079251-3d1c5ad1-412c-4263-a5a3-192f0e9c1c73.png)

-	le Client1 appartiendra au réseau « network1 »  d’adresse 172.18.0.0/24 et a pour adresse IP 172.18.0.3/24
![image](https://user-images.githubusercontent.com/56343178/172079308-0c1cf859-8c5f-4031-9f83-ec852672d98a.png)

 
On peut voir que le Client1 a bien reçu une adresse IP appartenant au réseau défini précédemment : 172.18.0.3
 
-	Le FireWall appartiendra au deux réseaux 172.17.0.0/24 et 172.18.0.0//24 et a donc les deux adresses IP suivantes : 172.0.17.2/24 et 172.0.18.2/24
![image](https://user-images.githubusercontent.com/56343178/172082016-3406a4cb-7cea-4aa9-b62f-68c1f9273ac6.png)


# III.	Test de la maquette

On vérifie d’abord que le Client a bien accès au serveur Web avant l’activation du pare-feu :
	Client :
 ![image](https://user-images.githubusercontent.com/56343178/172082040-d94d511e-cd58-49b1-b77d-4d5c2902659c.png)

Pour tester le Pare-feu, on teste donc la règle définie précédemment dans le fichier fw.sh qui permet d’interdire l’accès au site au Client en activant le pare-feu puis en réessayant de faire un curl avec le Client. 
Cependant, je n’ai pas réussi à appliquer cette règle sur le Client car je n’arrive pas à définir les deux  adresses IP du Firewall comme passerelle par défaut des containers Server et Client pour qu’il agisse comme un routeur. Donc j’ai testé la commande « curl » directement à partir du Firewall :
 ![image](https://user-images.githubusercontent.com/56343178/172082079-75ca3a21-0977-414a-babe-b52a337ced83.png)

On active maintenant le pare feu en exécutant le fichier fw.sh contenant la règle puis on refait un curl :
 ![image](https://user-images.githubusercontent.com/56343178/172082094-71e0b24f-8df0-4d22-829a-29cb80fac3f4.png)

On voit bien que l’accès est maintenant bloqué.









IV.	Docker Compose
On commence par installer Docker Compose avec la commande :  
« sudo curl -L https://github.com/docker/compose/releases/download/v2.0.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose »
 
On modifie les droits en exécution avec la commande :  « sudo chmod +x /usr/local/bin/docker-compose »
 Puis on vérifie l’installation avec la commande « docker-compose –version »
 
Puis on fait « docker-compose build »:
 
Et enfin « docker-compose up » :
 
