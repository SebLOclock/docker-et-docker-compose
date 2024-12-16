# Comprendre Docker et Docker Compose

## Objectifs

- Comprendre les concepts de base de Docker et Docker Compose.
- Apprendre à créer, gérer et utiliser des conteneurs Docker.
- Mettre en œuvre une application multi-conteneurs avec Docker Compose.
- Apprendre à entrer dans un conteneur Docker pour modifier des fichiers de configuration.

## Les notions

### Qu'est-ce que Docker ?

Docker est une plateforme permettant de développer, expédier et exécuter des applications dans des conteneurs. Les conteneurs permettent d'encapsuler une application et toutes ses dépendances dans une seule unité de déploiement. Voici quelques concepts clés de Docker :

*Image Docker :* Un modèle en lecture seule qui décrit les étapes pour créer un conteneur.
*Conteneur Docker :* Une instance en cours d'exécution d'une image Docker.
*Dockerfile :* Un fichier de texte contenant une série d'instructions pour construire une image Docker.
*Registry :* Un service de stockage et de distribution d'images Docker (ex: Docker Hub).

### Qu'est-ce que Docker Compose ?

Docker Compose est un outil pour définir et gérer des applications multi-conteneurs. Avec Docker Compose, vous pouvez utiliser un fichier YAML pour configurer les services de votre application. Ensuite, une seule commande vous permet de créer et de lancer tous les services spécifiés dans votre configuration.

*Service :* Une définition de conteneur spécifique dans un fichier Docker Compose.
*Volume :* Un mécanisme pour stocker des données persistantes générées par et utilisées par des conteneurs.
*Network :* Une couche de connectivité permettant aux conteneurs de communiquer entre eux.

## Passons à la pratique

L'objectif va être ici de mettre en place une application Node.js simple avec Docker et Docker Compose. L'application sera composée d'un service web Node.js et d'un service de base de données PostgreSQL.

N'hésitez pas à consulter la [CHEATSHEET](./CHEATSHEET.MD) pour vous aider dans la réalisation de ce challenge.

### Étape 1 : Création d'une Image Docker

1. Créez un répertoire pour votre projet :

```bash
mkdir docker_project
cd docker_project
```

`mkdir docker_project` : Crée un nouveau répertoire nommé docker_project.
`cd docker_project` : Change le répertoire de travail actuel pour docker_project.

2. Créez un fichier Dockerfile avec le contenu suivant :

```dockerfile
# Utiliser l'image de base officielle de Node.js
FROM node:14

# Définir le répertoire de travail dans le conteneur
WORKDIR /app

# Copier package.json et package-lock.json
COPY package*.json ./

# Installer les dépendances
RUN npm install

# Copier le reste du code de l'application
COPY . .

# Exposer le port sur lequel l'application va écouter
EXPOSE 3000

# Démarrer l'application
CMD ["node", "app.js"]
```

`FROM node:14` : Utilise l'image officielle de Node.js version 14 comme image de base.
`WORKDIR /app` : Définit /app comme répertoire de travail dans le conteneur.
`COPY package*.json ./` : Copie les fichiers package.json et package-lock.json dans le répertoire de travail du conteneur.
`RUN npm install` : Exécute la commande npm install pour installer les dépendances de l'application.
`COPY . .` : Copie tous les fichiers du répertoire actuel dans le répertoire de travail du conteneur.
`EXPOSE 3000` : Expose le port 3000 sur lequel l'application va écouter.
`CMD ["node", "app.js"]` : Définit la commande à exécuter lorsque le conteneur démarre.

3. Créez un fichier package.json pour une application Node.js simple :

```json
{
  "name": "docker_project",
  "version": "1.0.0",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "config": "^3.3.6"
  }
}
```

4. Créez un fichier config/default.json pour la configuration de l'application :

```json
{
  "appName": "Docker Application"
}
```

5. Créez un fichier app.js qui sera le point d'entrée de votre application :

```javascript
const http = require('http');
const config = require('config');

const hostname = '0.0.0.0';
const port = 3000;
const appName = config.get('appName');

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end(`Welcome to ${appName}\n`);
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

6. Construisez l'image Docker :

```bash
docker build -t mynodeapp .
```

`docker build` : Construire une image Docker.
`-t mynodeapp` : Taguer l'image avec le nom mynodeapp.
`.` : Utiliser le Dockerfile et les fichiers du répertoire actuel pour construire l'image.

7. Exécutez un conteneur à partir de l'image :

```bash
docker run -p 3000:3000 mynodeapp
```

`docker run` : Démarrer un nouveau conteneur.
`-p 3000:3000` : Mapper le port 3000 du conteneur sur le port 3000 de l'hôte.
`mynodeapp` : Utiliser l'image mynodeapp pour créer le conteneur.

8. Tester l'application

Ouvrez un navigateur web et accédez à http://localhost:3000 pour voir le message "Welcome to Docker Application".

Lorsqu'on à indquer le mapping du port 3000 du conteneur sur le port 3000 de l'hôte, cela permet à l'application Node.js de recevoir des requêtes HTTP sur le port 3000 de l'hôte.

### Étape 2 : Utilisation de Docker Compose

Docker Compose permet de définir et gérer des applications multi-conteneurs. Dans cette étape, nous allons créer un fichier docker-compose.yml pour définir les services de notre application Node.js et PostgreSQL.

Nous allons donc utiliser le conteneur Node.js que nous avons créé précédemment et ajouter un service de base de données PostgreSQL.

1. Créez un fichier docker-compose.yml dans le même répertoire :

```yaml
version: '3'
services:
  web:
    build: .
    ports:
      - "3000:3000"
```

`version: '3'` : Définir la version du fichier de configuration Docker Compose.
`services` : Définir les services qui composent l'application.
`web` : Nom du service.
`build: .` : Construire l'image à partir du Dockerfile dans le répertoire actuel.
`ports` : Mapper les ports.
`"3000:3000"` : Mapper le port 3000 de l'hôte sur le port 3000 du conteneur.

Rien de nouveau ici, nous avons simplement défini un service web qui utilise l'image Docker que nous avons construite précédemment et qui expose le port 3000.

2. Démarrez l'application avec Docker Compose :

```bash
docker-compose up
```

`docker-compose up` : Créer et démarrer les conteneurs définis dans docker-compose.yml.

### Étape 3 : Ajouter un Service de Base de Données

1. Modifiez le fichier docker-compose.yml pour ajouter un service de base de données (PostgreSQL) :

```yaml
version: '3'
services:
  web:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_HOST=database
      - DATABASE_PORT=5432
      - DATABASE_USER=postgres
      - DATABASE_PASSWORD=example
      - DATABASE_NAME=exampledb
    depends_on:
      - database

  database:
    image: postgres:13
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: example
      POSTGRES_DB: exampledb
    ports:
      - "5433:5432"
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

`environment` : Définir les variables d'environnement pour le service web afin de se connecter au service database.
`depends_on` : Spécifie que le service web dépend du service database, garantissant que le conteneur database est démarré avant web.
`image: postgres:13` : Utiliser l'image officielle de PostgreSQL version 13 pour le service database.
`volumes` : Définir des volumes pour persister les données de la base de données.

2. Démarrez les services :

```bash
docker-compose up
```

`docker-compose up` : Créer et démarrer les conteneurs définis dans docker-compose.yml.

3. Vérifiez que les conteneurs sont en cours d'exécution :

```bash
docker-compose ps
```

`docker-compose ps` : Afficher l'état des conteneurs définis dans docker-compose.yml.

### Étape 4 : Entrer dans un Conteneur pour Modifier un Fichier de Configuration

1. Identifiez l'ID du conteneur dans lequel vous souhaitez entrer :

```bash
docker ps
```

`docker ps` : Lister tous les conteneurs en cours d'exécution. Notez l'ID du conteneur que vous voulez inspecter.

2. Accédez à l'intérieur du conteneur :

```bash
docker exec -it <container_id> /bin/bash
```

`docker exec` : Exécuter une commande dans un conteneur en cours d'exécution.
`-it` : Exécuter en mode interactif avec un pseudo-terminal.
`<container_id>` : Remplacez <container_id> par l'ID réel de votre conteneur.
`/bin/bash` : Démarrer une session Bash dans le conteneur.

3. Modifiez le fichier de configuration souhaité. Par exemple, pour éditer le fichier config/default.json pour changer le nom de l'application :

```bash
nano /app/config/default.json
```

`nano /app/config/default.json` : Ouvrir le fichier default.json avec l'éditeur de texte nano.

Ah! Visiblement nano n'est pas installé dans le conteneur. En effet, l'image de base Node.js ne contient pas l'éditeur de texte nano. Mais on se rend compte qu'il s'agit d'un conteneur Linux. On peut donc utiliser le gestionnaire de paquets apt pour installer nano.

```bash
apt-get update
apt-get install nano
```

`apt-get update` : Mettre à jour les informations des paquets disponibles.
`apt-get install nano` : Installer l'éditeur de texte nano.

Mainenant que nano est installé, vous pouvez éditer le fichier de configuration :

```bash
nano /app/config/default.json
```


Après avoir fait les modifications nécessaires, sauvegardez et fermez l'éditeur. Par exemple, avec nano, vous pouvez sauvegarder les modifications en appuyant sur Ctrl + O et fermer l'éditeur avec Ctrl + X.

1. Redémarrez le conteneur pour appliquer les modifications :

```bash
exit
docker-compose restart web
```

`exit` : Quitter la session Bash et retourner à votre terminal local.
`docker-compose restart web` : Redémarrer le service web défini dans le fichier docker-compose.yml.

Retournez sur votre navigateur et actualisez la page pour voir les modifications apportées à l'application.

### Étape 5 : Nettoyage
Pour arrêter et supprimer les conteneurs, les réseaux et les volumes définis dans le fichier docker-compose.yml, utilisez la commande suivante :

```bash
docker-compose down
```

`docker-compose down` : Arrêter et supprimer les conteneurs, les réseaux et les volumes créés par Docker Compose.

## Conclusion
Ce challenge vous a introduit aux concepts de base de Docker et Docker Compose et vous a guidé à travers la création et l'exécution d'une application simple. Vous avez également appris comment entrer dans un conteneur Docker pour modifier des fichiers de configuration. Vous devriez maintenant avoir une meilleure compréhension de comment containeriser une application et orchestrer plusieurs conteneurs pour créer une application multi-services. Pour aller plus loin, explorez les volumes de données, les réseaux de conteneurs, et la gestion avancée des services avec Docker Compose.