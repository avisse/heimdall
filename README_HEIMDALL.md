# 🖥️ Projet – Heimdall : Portail centralisé des applications (Collectivité)

## 🎯 Contexte
Dans une collectivité locale de taille moyenne, la Direction des Systèmes d’Information (DSI) administre une dizaine d’applications critiques :

- **GLPI** : gestion du support IT  
- **Nextcloud** : partage et collaboration  
- **Rocket.Chat** : communication interne  
- **Grafana** et **Uptime Kuma** : supervision  
- **Portainer** et **phpMyAdmin** : administration technique  

Chaque outil est déployé via Docker sur des serveurs internes. Problème identifié :  
➡️ URLs multiples, ports différents, perte de temps, erreurs fréquentes et complexité pour les nouveaux arrivants.

## 🛠️ Objectif
Mettre en place un **portail centralisé simple et intuitif**, accessible via un navigateur, permettant de retrouver tous les outils en un clic.  
👉 Solution choisie : **Heimdall**, une application libre de gestion de raccourcis et tableau de bord.

## ⚙️ Étapes de mise en place

### 1. Création du projet
- Installation de Docker et Docker Compose sur une **VM Debian 12**  
- Création d’un dossier dédié : `/home/vboxuser/projet-ecf/heimdall/`  
- Initialisation d’un `docker-compose.yml` propre

### 2. Docker Compose
```yaml
version: "3.9"
services:
  heimdall:
    image: lscr.io/linuxserver/heimdall:latest
    container_name: heimdall
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Paris
    volumes:
      - ./config:/config
      - ./ssl:/config/nginx/ssl:ro
    ports:
      - "8080:80"
      - "8443:443"
    restart: unless-stopped
```
➡️ Les volumes `config/` et `ssl/` garantissent la persistance et la sécurisation via HTTPS.

### 3. Configuration & personnalisation
- Premier lancement : `docker compose up -d`  
- Accès via `http://IP_VM:8080` puis configuration de l’interface  
- Création de **tuiles par application** : GLPI, Nextcloud, Rocket.Chat, Grafana, Portainer…  
- Organisation par **catégories** : Support, Collaboration, Supervision, Admin  
- Ajout de fonds d’écran et logos

### 4. Sécurisation
- Ajout d’un certificat SSL (auto-signé pour la démo) dans `./ssl/`  
- Redirection HTTP → HTTPS  
- Restriction d’accès à l’interface Heimdall aux IP internes via firewall (pfSense)  

### 5. Documentation & sauvegarde
- Rédaction d’un README GitHub (architecture, déploiement, maintenance)  
- Script de sauvegarde du volume `config/` :  
```bash
#!/bin/bash
STAMP=$(date +%Y%m%d_%H%M)
tar -czf /backups/heimdall_config_${STAMP}.tar.gz -C ./config .
find /backups -type f -name "heimdall_config_*.tar.gz" -mtime +7 -delete
```

## ✅ Résultats obtenus
- **Centralisation** : un portail unique regroupant tous les outils critiques  
- **Gain de temps** : plus besoin de retenir URLs/ports  
- **Adoption** : onboarding facilité pour les nouveaux techniciens  
- **Robustesse** : configuration persistante et sécurisée via volumes et SSL  

## 📌 Bilan personnel
Ce projet m’a permis de :  
- Découvrir et déployer **Heimdall** avec Docker  
- Pratiquer la **sécurisation d’accès web** (SSL, firewall, reverse proxy)  
- Mettre en avant la **valeur métier** : simplifier l’expérience des utilisateurs IT  
- Produire un projet **clé en main et réutilisable** pour d’autres contextes  

---
✍️ Auteur : **Steve Avisse**
