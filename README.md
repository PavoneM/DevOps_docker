# DevOps_docker | Groupe 12

<b>1/ Créer une image docker pour faire tourner la partie paiement de TCF, en partant d'une image OS seul (pas de l'image toute faite mono qu'on trouve sur docker hub). Expérimentez avec la création en mode interactif si vous le souhaitez, puis faites un docker file. Conseil: mettez-le au point incrémentalement, ligne à ligne ;</b>

=> L'image se trouve dans la partie Payment du repository. Elle peut être construite grâçe à la commande 
  sudo docker build -t serpe/payment .
  
<b>2/ Créer une deuxième image docker pour faire tourner la partie J2EE. Vous pouvez partir d'une image OS seul, ou bien de l'image toute faite TomEE+, mais l'essentiel est que vous expérimentiez ;</b>

=> L'image se trouve dans la partie Tomee du repo. Elle peut être construite avec la commande 
  sudo docker build -t serpe/tomee .
  
Pour la compilation : Le dernier paramètre étant le dossier où se trouve le Dockerfile.
Le Dockerfile ira chercher à son même niveau d'arborescence le serveur.exe (déjà compilé à l'avance) pour l'image payment, et tcf-backend pour le serveur tomee.
  
Les deux images peuvent être respectivement être exécutées graçe aux commandes suivantes:

  - sudo docker run -d -p 9090:9090 serpe/payment
  - sudo docker run -d --net='host' -p 8080:8080 serpe/payment

L'option -d permet de mettre le serveur en background et ne pas monopoliser le terminal par l'action.
Le --net sert pour spécifier que le serveur tourne sur le même réseau que l'image du payment.
Le -p port:port sert pour exposer les ports. Nous l'avons déjà fait dans le Dockerfile des deux images.

<b>3/ Depuis votre laptop, vous devez pouvoir utiliser le CLI pour invoquer un web service qui utilise le paiement. Le tout avec les containers docker Payment et WebServices, soit en local, soit distribués sur plusieurs machines (vous pouvez utiliser la VM i3s pour hoster un des containers)</b>

Une fois les images lancées (elles tournent en local), vous pouvez lancer le client avec mvn exec:java.
Si en revanche vous voulez exécuter sur plusieurs machines, cela est possible en changeant les ports en dur dans le BankAPI du serveur tomee, ainsi qu'en donnant les paramètres au client de la façon suivante :
  - mvn exec:java -Dexec.args="ip"
  - mvn exec:java -Dexec.args="ip port"
  
  Vous pouvez aussi accéder aux différents services à partir du site http://ip:8080/tcf-backend/

Maintenant, en guise de réflexion pour un futur développement, nous pouvons imaginer ne plus vouloir changer les ports en dur dans le code. 3 façons envisageables possibles :
  - Changer le code pour que nous puissions passer en paramètre un IP à partir des arguments du main. Puis ne pas mettre le CMD dans le dockerfile du serveur tomee pour passer la commande d'exécution à partir du "docker run" et ainsi passer en paramètre l'IP.
  - Effectuer lors de la copie du tcf-backend.war un remplacement (commande sed par exemple) du fichier BankAPI en dézippand puis en re-zippant l'archive à partir du Dockerfile.
  - Créer une image qui tourne en background et qui se lie au serveur, se chargeant d'aller modifier l'IP.

<b>4/ En utilisant Docker Compose, vous définissez un environnement contenant vos 2 images docker précédemment créées. Vous voyez ainsi la valeur de containeriser, votre code pourra tourner dans une image docker locale, distante ou inclue dans un environnement qui provisionne l'ensemble de votre système en un seul coup.</b>

Tout d'abord, il vous faut installer le docker-compose à partir du guide d'installation suivant:

https://docs.docker.com/compose/install/

Placez-vous dans le dossier compose du repository.
Puis vous pouvez lancer le container contenant les deux images grâçe à la commande suivante :

sudo docker-compose up 

Le serveur de payment ainsi que le serveur tomee devraient se lancer en parallèle. Vous pouvez ainsi accéder aux différents services comme précédemment.

<b>Nous tenons à préciser que tout est déjà présent sur la VM au cas où il y aurait des soucis dans le répertoire ~/Devops/DevOps_docker/.</b>
