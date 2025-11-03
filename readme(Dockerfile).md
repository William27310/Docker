---

## 🧱 Dockerfile du service web (PHP + Apache)

Le service `web` utilise un **Dockerfile** situé à la racine du projet.  
Ce fichier définit comment construire l’image qui fera tourner ton application PHP avec Apache.

Voici le contenu à créer dans un fichier nommé **`Dockerfile`** (sans extension) :

```dockerfile
# Étape 1 : Choisir une image de base
FROM php:8.2-apache

# Étape 2 : Installer les extensions PHP nécessaires (ex : MySQL, zip, etc.)
RUN docker-php-ext-install pdo pdo_mysql mysqli

# Étape 3 : Activer le module Apache "rewrite" (utile pour les frameworks comme Laravel ou Symfony)
RUN a2enmod rewrite

# Étape 4 : Copier le contenu du projet dans le dossier web du conteneur
COPY . /var/www/html/

# Étape 5 : Définir les permissions pour Apache
RUN chown -R www-data:www-data /var/www/html

# Étape 6 : Exposer le port 80 (déjà fait automatiquement par l’image Apache, mais bon à rappeler)
EXPOSE 80

| Étape                            | Description                                                            |
| -------------------------------- | ---------------------------------------------------------------------- |
| `FROM php:8.2-apache`            | Utilise l’image officielle PHP avec Apache intégré.                    |
| `RUN docker-php-ext-install ...` | Installe les extensions PHP nécessaires à ton app.                     |
| `RUN a2enmod rewrite`            | Active le module `mod_rewrite` d’Apache (utile pour les URLs propres). |
| `COPY . /var/www/html/`          | Copie ton code source dans le dossier web du conteneur.                |
| `RUN chown ...`                  | Donne les bons droits au serveur Apache.                               |
| `EXPOSE 80`                      | Rend le conteneur accessible sur le port 80.                           |

✅ Résumé

Ton environnement est maintenant complet :

docker-compose.yml → orchestre les services

Dockerfile → définit le serveur PHP/Apache

dockercompose.md → documente ton projet

Tu peux lancer l’ensemble avec :

docker compose up -d