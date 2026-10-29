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
