# 🗺️ Guide & Roadmap: Installation SAS Studio sur Client PC / Server

Ce document contient la procédure standard (SOP) pour installer et déployer **SAS Studio** chez n'importe quel client.

---

## 📋 Table des Matières
1. [Prérequis Système](#1-prérequis-système)
2. [Installation & Personnalisation de la Marque](#2-installation--personnalisation-de-la-marque)
3. [Configuration de la Base de Données](#3-configuration-de-la-base-de-données)
4. [Build de Production & Lancement](#4-build-de-production--lancement)
5. [Guide de Livraison au Client](#5-guide-de-livraison-au-client)
6. [Résolution des Problèmes Courants](#6-résolution-des-problèmes-courants)

---

## 1. Prérequis Système

Avant de commencer l'installation sur le PC ou serveur du client, vérifiez la présence des outils suivants :

| Outil | Version Minimale | Commande de Vérification |
| :--- | :--- | :--- |
| **Git** | `v2.x` | `git --version` |
| **Node.js** | `v18.x` ou `v20.x` | `node -v` |
| **Yarn** | `v1.22.x` | `yarn -v` |
| **Docker** | `Docker Desktop` ou `Docker Engine` | `docker --version` |

---

## 2. Installation & Personnalisation de la Marque

### Étape 2.1 : Cloner le Répertoire
```powershell
git clone -c core.symlinks=true https://github.com/calcom/cal.diy.git sas-studio
cd sas-studio
```

### Étape 2.2 : Installer les Dépendances
```powershell
yarn install
```

### Étape 2.3 : Configurer le Fichier `.env`
Dupliquez le fichier `.env.example` en `.env` :
```powershell
cp .env.example .env
```

Modifiez les variables de marque et de connexion :
```env
# Informations de la Marque du Client
NEXT_PUBLIC_APP_NAME="SAS Studio - [Nom du Client]"
NEXT_PUBLIC_COMPANY_NAME="Nom du Client"
NEXT_PUBLIC_SUPPORT_MAIL_ADDRESS="support@clientdomain.com"

# URLs de l'application
NEXT_PUBLIC_WEBAPP_URL="http://localhost:3000"
NEXTAUTH_URL="http://localhost:3000"

# Connexion PostgreSQL (Docker Container)
DATABASE_URL="postgresql://unicorn_user:magical_password@127.0.0.1:5454/calendso"
DATABASE_DIRECT_URL="postgresql://unicorn_user:magical_password@127.0.0.1:5454/calendso"
```

### Étape 2.4 : Remplacer les Logos
- Remplacez le logo du header : `apps/web/public/cal-logo-word.svg`
- Remplacez l'icône de navigateur : `apps/web/public/favicon.ico`

---

## 3. Configuration de la Base de Données

### Étape 3.1 : Démarrer le Conteneur PostgreSQL
Dans `docker-compose.yml`, assurez-vous que le port `5454:5432` est exposé, puis lancez :
```powershell
docker-compose up -d database
```

### Étape 3.2 : Exécuter les Migrations et Injecter les Données
```powershell
yarn db-deploy
yarn db-seed
```

---

## 4. Build de Production & Lancement

Pour garantir des performances maximales et une navigation instantanée (< 0.05s par page) :

### Étape 4.1 : Compiler le Build de Production
```powershell
yarn build
```

### Étape 4.2 : Démarrer le Serveur de Production
```powershell
yarn workspace @calcom/web start
```
L'application sera accessible sur **http://localhost:3000**.

---

## 5. Guide de Livraison au Client

1. **Identifiants Administrateur de Départ** :
   - **Email** : `pro@example.com`
   - **Mot de passe** : `pro`
2. **Accès au Panneau d'Administration** :
   - Naviguez vers : `http://localhost:3000/settings/admin`
   - Modifiez l'adresse email et le mot de passe de l'administrateur.
3. **Création du Premier Type d'Événement** :
   - Configurez un premier calendrier de rendez-vous (ex: Consultation 30 min).

---

## 6. Résolution des Problèmes Courants

| Problème / Symptôme | Cause | Solution |
| :--- | :--- | :--- |
| `EADDRINUSE: :::3000` | Le port 3000 est déjà utilisé par une autre instance. | Arrêtez le processus en cours sur le port 3000 (`Ctrl+C`). |
| `Can't reach database server at localhost:5454` | Résolution IPv6 sur Windows. | Utilisez `127.0.0.1:5454` à la place de `localhost:5454` dans `.env`. |
| Lenteur de navigation | Application lancée en mode `yarn dev`. | Exécutez `yarn build` puis `yarn workspace @calcom/web start`. |
