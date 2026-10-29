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
