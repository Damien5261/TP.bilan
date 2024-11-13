Installation de docker et docker compose : 

Pour celà j'ai utilisé ce script bash :

#!/bin/bash

# Vérifie si l'utilisateur est root
if [ "$EUID" -ne 0 ]; then
  echo "Veuillez exécuter ce script en tant que root ou avec sudo."
  exit 1
fi

# Met à jour les paquets et installe les dépendances nécessaires
echo "Mise à jour des paquets et installation des dépendances..."
apt update && apt install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Ajoute la clé GPG officielle de Docker
echo "Ajout de la clé GPG de Docker..."
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Ajoute le dépôt Docker à la liste des sources
echo "Ajout du dépôt Docker..."
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

# Met à jour la liste des paquets avec le dépôt Docker et installe Docker
echo "Installation de Docker..."
apt update && apt install -y docker-ce docker-ce-cli containerd.io

# Vérifie si Docker est installé correctement
echo "Vérification de l'installation de Docker..."
docker --version
if [ $? -ne 0 ]; then
  echo "L'installation de Docker a échoué."
  exit 1
fi

# Installe Docker Compose
DOCKER_COMPOSE_VERSION="2.1.1" # Dernière version stable à vérifier
echo "Installation de Docker Compose version $DOCKER_COMPOSE_VERSION..."
curl -L "https://github.com/docker/compose/releases/download/v$DOCKER_COMPOSE_VERSION/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# Vérifie si Docker Compose est installé correctement
echo "Vérification de l'installation de Docker Compose..."
docker-compose --version
if [ $? -ne 0 ]; then
  echo "L'installation de Docker Compose a échoué."
  exit 1
fi

# Active et démarre le service Docker
echo "Activation et démarrage du service Docker..."
systemctl enable docker
systemctl start docker

# Ajoute l'utilisateur actuel au groupe docker (optionnel)
read -p "Voulez-vous ajouter l'utilisateur $(whoami) au groupe docker ? (y/n) " -n 1 -r
echo
if [[ $REPLY =~ ^[Yy]$ ]]; then
    usermod -aG docker $(whoami)
    echo "L'utilisateur $(whoami) a été ajouté au groupe docker. Veuillez vous déconnecter et vous reconnecter pour appliquer les changements."
fi

echo "Installation de Docker et Docker Compose terminée avec succès."

