

# Prérequis du TP : 

-VM debian 11 ou 12
-Une Connexion internet pour télécharger docker, docker-compose et les images sur docker-hub
-Une liaison SSH pour le lier à Git

Pour commencer, j'ai utilisé ce script bash dans mon fichier install_docker.sh :

# Installation de docker

il faudra réaliser les commandes suivantes :

"apt install git"
"git clone https://github.com/Damien5261/TP.bilan"
la commande suivante : "ls" va nous permettre de voir si le répertoire a été installer.
"cd TP.bilan" pour se rendre dans le répertoire
"chmod +x install.docker" va permettre de rendre le fichier éxécutable
Et il suffit maintenant de l'installer avec la commande "./install.docker"
 
# Installe de Docker Compose
DOCKER_COMPOSE_VERSION="2.1.1" # Dernière version stable à vérifier
echo "Installation de Docker Compose version $DOCKER_COMPOSE_VERSION..."
curl -L "https://github.com/docker/compose/releases/download/v$DOCKER_COMPOSE_VERSION/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# Vérifier si Docker Compose est installé correctement
echo "Vérification de l'installation de Docker Compose..."
docker-compose --version
if [ $? -ne 0 ]; then
  echo "L'installation de Docker Compose a échoué."
  exit 1
fi

# Active et démarre le service Docker
systemctl enable docker
systemctl start docker

# Ajout de l'utilisateur actuel au groupe docker (optionnel)
read -p "Voulez-vous ajouter l'utilisateur $(whoami) au groupe docker ? (y/n) " -n 1 -r
echo
if [[ $REPLY =~ ^[Yy]$ ]]; then
    usermod -aG docker $(whoami)
    echo "L'utilisateur $(whoami) a été ajouté au groupe docker. Veuillez vous déconnecter et vous reconnecter pour appliquer les changements."
fi

echo "Installation de Docker et Docker Compose terminée avec succès."

# Installation de wordpress

Pour installer wordpress avec docker j'ai utilisé deux commandes : 
- docker pull wordpress : pour télécharger l'image de wordpress grâce au docker hub
-docker run -p 8080:80 -d wordpress : pour le déployer

Le -p sert à désigner le port que nous souhaitons attribuer au container. Ceci est complètement modifiable à notre guise
Le -d sert à lancer le container

Maintenant il suffit de tester en rentrant son adresse ip dans un navigateur (commande "ip a" pour connaître son adresse") comme ceci : 192.168.x.x:8080
Vous arriverez sur l'interface de wordpress.

# Installation de zabbix : 

version: '3.5'

services:
  zabbix-db:
    image: zabbix/zabbix-server-mysql:latest
    container_name: zabbix-db
    environment:
      MYSQL_ROOT_PASSWORD: zabbix_password
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbix_password
      MYSQL_DATABASE: zabbix
    volumes:
      - zabbix-db-data:/var/lib/mysql
    networks:
      - zabbix-net

  zabbix-server:
    image: zabbix/zabbix-server-mysql:latest
    container_name: zabbix-server
    environment:
      DB_SERVER_HOST: zabbix-db
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbix_password
      MYSQL_DATABASE: zabbix
      ZBX_JAVA_GATEWAY: zabbix-java-gateway
    depends_on:
      - zabbix-db
    ports:
      - "10051:10051"
    networks:
      - zabbix-net

  zabbix-web:
    image: zabbix/zabbix-web-nginx-mysql:latest
    container_name: zabbix-web
    environment:
      ZBX_SERVER_HOST: zabbix-server
      DB_SERVER_HOST: zabbix-db
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbix_password
      MYSQL_DATABASE: zabbix
    ports:
      - "80:8081"
    depends_on:
      - zabbix-server
    networks:
      - zabbix-net

  zabbix-java-gateway:
    image: zabbix/zabbix-java-gateway:latest
    container_name: zabbix-java-gateway
    networks:
      - zabbix-net

networks:
  zabbix-net:

volumes:
  zabbix-db-data:


