# Environnement de développement DWWM

Mon environnement de travail de développeur web : Docker Desktop et les conteneurs de la formation.

## Installation

### Mon poste (vérifié le 29/10/2026)

| Élément        | Valeur                                    |
| -------------- | ----------------------------------------- |
| Système        | Windows 11 Famille, version 25H2, 64 bits |
| Mémoire vive   | 32 Go                                     |
| Virtualisation | Activée                                   |
| WSL            | 3.0.3.0                                   |

### Docker Desktop

1. Téléchargé depuis la documentation officielle : https://docs.docker.com/desktop/setup/install/windows-install/
2. Installé pour mon compte (mode proposé par défaut), avec le moteur WSL 2
3. Contrat accepté au premier démarrage (gratuit pour un usage personnel et pour l'éducation)

Versions installées : Docker 29.8.2, Docker Compose v5.5.1.

### Vérification

La commande `docker run hello-world` affiche « Hello from Docker! » : l'installation fonctionne.

## Servir le portfolio avec Apache

Le portfolio v5 (`C:\dev\portfolio-v5`) est servi par l'image officielle `httpd:2.4`, sur http://localhost:8080 :

```powershell
docker run -d --name portfolio-apache -p 8080:80 -v "C:\dev\portfolio-v5\dist:/usr/local/apache2/htdocs:ro" httpd:2.4
```

- Mettre le site à jour : `npm run build` dans le portfolio, puis recharger la page (Ctrl + F5).
- Le dossier `dist` est monté en lecture seule (`:ro`) : Apache ne peut pas le modifier.
- Arrêter et supprimer le serveur : `docker rm -f portfolio-apache`.

## Base de données MySQL et Adminer

Réseau `dwwm`, volume `dwwm-donnees`, MySQL 8.4 et Adminer :

```powershell
docker network create dwwm
docker volume create dwwm-donnees
docker run -d --name dwwm-mysql --network dwwm -p 3307:3306 `
    -e MYSQL_ROOT_PASSWORD=<mot-de-passe-root> `
    -e MYSQL_DATABASE=dwwm `
    -e MYSQL_USER=dwwm `
    -e MYSQL_PASSWORD=<mot-de-passe-dwwm> `
    -v dwwm-donnees:/var/lib/mysql `
    mysql:8.4
docker run -d --name dwwm-adminer --network dwwm -p 8081:8080 adminer:6
```

- Adminer : http://localhost:8081 (serveur `dwwm-mysql`, utilisateur `dwwm`, base `dwwm`).
- Depuis un outil de Windows : `localhost`, port 3307.
- Test de persistance réussi le 29/10/2026 : table `contacts` retrouvée après `docker stop` et `docker rm` de `dwwm-mysql`, puis recréation du conteneur.

## Construire le portfolio avec Node.js dans un conteneur

Depuis `C:\dev\portfolio-v5`, dans PowerShell :

```powershell
docker run --rm -v "${PWD}:/app" -v /app/node_modules -w /app node:24-alpine sh -c "npm ci && npm run build"
```

- Node.js 24 (image `node:24-alpine`), sans dépendre de la version installée sur le poste.
- `-v /app/node_modules` : le conteneur installe ses propres dépendances pour Linux ; le `node_modules` de Windows (programmes compilés pour Windows) n'est pas utilisé.
- Le dossier `dist` est écrit dans le projet, prêt à être servi par Apache.

## Démarrer l'environnement avec Docker Compose

Services décrits dans `compose.yaml` : `web` (Apache 2.4), `db` (MySQL 8.4), `adminer` (Adminer 6).
Ils remplacent les commandes `docker run` d'Apache et de MySQL des parties précédentes.

1. Copier le modèle des variables avec `Copy-Item .env.example .env`, puis mettre ses propres mots de passe de test dans `.env`.
2. Construire le portfolio (`npm run build` dans `C:\dev\portfolio-v5`), puis démarrer avec `docker compose up -d`.

| Service              | Adresse                              |
| -------------------- | ------------------------------------ |
| Portfolio (Apache)   | http://localhost:8080                |
| Adminer              | http://localhost:8081 (serveur `db`) |
| MySQL depuis Windows | `localhost`, port 3307               |

- Vérifier : `docker compose ps`, `docker compose logs db`
- Arrêter : `docker compose down` (les données sont conservées dans le volume)
- ⚠️ `docker compose down -v` supprime aussi le volume : toutes les données de la base sont effacées.
