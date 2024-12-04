

# Prérequis du TP : 

-VM debian 11 ou 12

-Une Connexion internet pour télécharger docker, docker-compose et les images sur docker-hub

-Une liaison SSH pour le lier à Git

# Installation de Git

En étant en utilisateur root

Voici les commandes à faire :

"apt install git"

"git clone https://github.com/Damien5261/TP.bilan" pour installer le répertoire Git contenant mes fichiers 

la commande suivante : "ls" va nous permettre de voir si le répertoire a été installer.

"cd TP.bilan" pour se rendre dans le répertoire , et avec une nouvelle fois la commande "ls" il devrait y avoir les fichiers suivant : install.docker , install.docker-compose , wordpress ,zabbix, README.MD

Pour tout ses fichiers sauf README.MD il faudra faire la commande "chmod +x nom du fichier" par exemple : "chmod +x wordpress" ou "chmod +x install.docker" etc.. pour permettre aux fichiers d'être éxécutable.

# Installation de docker

Il suffit juste maintenant d'installer le fichier avec la commande "./install.docker"

Il est possible de vérifier si docker est bien actif avec la commande "systemctl status docker"
 
# Installe de Docker Compose

De même il faudra juste l'installer avec la commande "./install.docker-compose" pour éxécuter.

# Installation de wordpress

Pour installer wordpress il faut faire de même , c'est à dire "./wordpress"

Explication des commandes utiliser dans mon fichier wordpress :

-docker pull wordpress : pour télécharger l'image de wordpress grâce au docker hub

-docker run -p 8080:80 -d wordpress : pour le déployer

Le -p sert à désigner le port que nous souhaitons attribuer au container (en l'occurence le port 80) .Ceci est complètement modifiable à notre guise

Le -d sert à lancer le container

Maintenant il suffit de tester en rentrant son adresse ip dans un navigateur (commande "ip a" pour connaître son adresse") comme ceci : 192.168.x.x:8080

Vous arriverez sur l'interface de wordpress.

# Installation de zabbix : 

L'installation de zabbix est pareil que les autres :

Il suffit de rentrer la commande ./zabbix


