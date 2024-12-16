# Docker Cheatsheet

<p align="center" width="100%">
    <img height="200px" src="./docker-logo.png">
</p>

## Docker - images

```bash
# Lister les images de conteneurs présentes sur votre machine
docker images

# Build une image docker dans le répertoire courant
# (en partant du principe que l'on a un fichier Dockerfile valide)
# rappel : l'option `-t` permet d'ajouter un tag à l'image
docker build -t pseudo/monimage:0.0.1 .
```

## Docker - conteneurs

```bash
# Lister tous les conteneurs qui tournent (status : running)
docker ps
# Lister tous les conteneurs, y compris les conteneurs arrêtés
docker ps -a

# Supprimer tous les conteneurs docker arrêtés
docker rm -f $(docker ps -aq)
# Supprimer toutes les images de conteneurs docker
docker rmi -f $(docker images -aq)
# On fait le ménage (fichiers temporaires, images non utilisées, conteneurs arrêtés mais pas supprimés, etc.)
docker system prune -a

# Démarrer un conteneur depuis une image donnée 
docker run pseudo/monimage:0.0.1
# On peut également spécifier l'option --rm (pour "remove"), 
# ce qui permet de supprimer le conteneur une fois le conteneur arrêté
docker run --rm pseudo/monimage:0.0.1
# On peut aussi spécifier le nom de notre choix pour le conteneur
docker run --name mon_super_conteneur pseudo/monimage:0.0.1

# Afficher les logs d'un conteneur
docker logs <nom_du_conteneur | id_conteneur>

# Executer des commandes (ici on exécute un shell, bash, de manière interactive)
docker exec -it <nom_du_conteneur | id_conteneur> bash

# Arrêter un conteneur
docker stop <nom_du_conteneur | id_conteneur>
```

## Docker Compose

**Rappel : pour lancer des commandes du type `docker compose` (`docker-compose` marche aussi), il faut vous positionner dans le répertoire qui contient le fichier `docker-compose.yml`.**

```bash
# Arrêter et supprimer tous les conteneurs du docker-compose.yml 
docker compose down

# Démarrer (et build les images des conteneurs, si besoin) les
# conteneurs du fichier docker-compose.yml
# On ajoute également l'option `-d` pour `--detach` qui permet 
# de ne pas monopoliser le terminal
docker compose up -d

# Executer une commande dans un conteneur du docker-compose.yml
# Ici on prends l'exemple du conteneur nommé `web` et on exécute
# la commande zsh (comme vu lors du cours de Laravel) 
docker compose exec web zsh
```

## "docker compose down" be like :
![docker compose down](https://i.imgflip.com/7wepjq.gif)

## "docker compose up -d" be like :
![docker compose up -d](https://i.imgflip.com/7wepku.gif)
