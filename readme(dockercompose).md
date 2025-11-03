# 🚀 Docker Compose – Environnement GMAO (PHP / MySQL / phpMyAdmin)

## 🧩 Description du projet
Ce projet met en place un environnement complet pour une application **GMAO (Gestion de Maintenance Assistée par Ordinateur)**.  
Il utilise **Docker Compose** pour orchestrer trois services :
- **PHP/Apache** : serveur web pour exécuter l’application.
- **MySQL** : base de données principale.
- **phpMyAdmin** : interface graphique pour gérer la base de données.

---

## 📁 Structure du projet

├── docker-compose.yml # Fichier principal de configuration Docker
├── db/
│ └── init/ # (Optionnel) Scripts SQL exécutés à la création de la base
├── src/ ou www/ # (Optionnel) Code source PHP de ton application
└── dockercompose.md # Ce fichier de documentation


---

## ⚙️ Services définis

### 1. 🖥️ Service `web`
- **Image** : construite à partir du `Dockerfile` local.  
- **Ports** : `8080:80` → accessible sur [http://localhost:8080](http://localhost:8080)  
- **Volume** : synchronise le dossier du projet avec `/var/www/html` dans le conteneur.  
- **Variables d’environnement** : permet à PHP de se connecter à la base MySQL.  

### 2. 🗄️ Service `db`
- **Image** : `mysql:8`  
- **Port** : `3306:3306`  
- **Volume** : `db_data` pour garder les données même après suppression du conteneur.  
- **Initialisation** : peut exécuter des fichiers `.sql` dans `./db/init/`.  

### 3. 🌐 Service `phpmyadmin`
- **Image** : `phpmyadmin/phpmyadmin`  
- **Port** : `8081:80` → accessible sur [http://localhost:8081](http://localhost:8081)  
- **Connexion** : automatique à la base `db` sur le port `3306`.

---

## ▶️ Démarrage de l’environnement

1. **Construire et lancer les conteneurs :**
   ```bash
   docker compose up -d

**Vérifier les conteneurs actifs :**

docker ps

**Accéder aux services :**

Application web → http://localhost:8080

phpMyAdmin → http://localhost:8081

**Arrêter et supprimer les conteneurs :**

docker compose down

## 💾 Persistance des données

Les données MySQL sont stockées dans un volume Docker nommé db_data, ce qui les préserve même après un docker compose down.

## 🔧 Variables d’environnement principales

Variable	Service	Description
MYSQL_ROOT_PASSWORD	db	Mot de passe administrateur MySQL
MYSQL_DATABASE	db / web	Nom de la base créée automatiquement
MYSQL_USER	web	Utilisateur MySQL
MYSQL_PASSWORD	web	Mot de passe de l’utilisateur MySQL

## 🧹 Nettoyage

**Pour supprimer complètement les conteneurs, les images et les volumes associés :**

docker compose down --volumes --rmi all