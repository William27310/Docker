#1044 - Accès refusé pour l'utilisateur: 'docker'@'@%'. Base 'GMAO'

docker exec -it mysql-db-Docker mysql -u root -p

mdp = rootpassword

Supprime l'utilisateur corrompu -> 

DROP USER IF EXISTS 'docker'@'%';
DROP USER IF EXISTS 'docker'@'localhost';

Recrée l'utilisateur correctement ->

CREATE USER 'docker'@'%' IDENTIFIED BY 'dockerpass';
CREATE USER 'docker'@'localhost' IDENTIFIED BY 'dockerpass';

Donne tous les droits sur la base sql GMAO ->

GRANT ALL PRIVILEGES ON gmao.* TO 'docker'@'%';
GRANT ALL PRIVILEGES ON gmao.* TO 'docker'@'localhost';
FLUSH PRIVILEGES;

Teste la connexion avec l’utilisateur docker ->

docker exec -it mysql-db-Docker mysql -u docker -p gmao

========================================

docker-compose.yml :

services:

➡️ C’est ici qu’on définit tous les conteneurs de ton environnement (ici : web, db, et phpmyadmin).

==

  web:
    build: ./php
    container_name: php-apache
    ports:
      - "8080:80"
    volumes:
      - ./src:/var/www/html
    depends_on:
      - db

==

build: ./php.

Docker va construire une image à partir du Dockerfile dans le dossier php/.C’est là qu’on installe PHP et Apache.

==

container_name: php-apache.

Nom personnalisé du conteneur (sinon Docker en génère un aléatoire).

==

ports: "8080:80".

Lie le port 80 du conteneur (Apache) au port 8080 de ton PC → ton site est visible sur http://localhost:8080.

==

volumes: ./src:/var/www/html.

Monte ton dossier src (ton code PHP) à l’intérieur du conteneur, dans /var/www/html (dossier web d’Apache).

==

depends_on: db
Démarre le conteneur db (MySQL) avant web.

========================================

  db:
    image: mysql:8.0
    container_name: mysql-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: projetdb
      MYSQL_USER: user
      MYSQL_PASSWORD: userpassword
    volumes:
      - db_data:/var/lib/mysql
    ports:
      - "3306:3306"

==

image: mysql:8.0 = Utilise l’image officielle MySQL version 8.0.

==

container_name = Nom du conteneur MySQL.

==

no (par défaut) = Le conteneur ne redémarre pas automatiquement.

restart: always = Si le conteneur s’arrête, Docker le relance automatiquement.

unless-stopped = Redémarre automatiquement sauf si tu l’as arrêté manuellement (docker stop).

on-failure = Redémarre seulement s’il s’arrête avec une erreur.

==

environment = Variables d’environnement pour initialiser MySQL (mot de passe root, base par défaut, utilisateur, etc.).

==

volumes = Sauvegarde les données dans un volume Docker (db_data), pour éviter de tout perdre à chaque arrêt.

==

ports: "3306:3306" = Rend la base MySQL accessible localement (utile pour se connecter depuis un outil externe comme DBeaver).

==

image: phpmyadmin/phpmyadmin = Utilise l’image officielle phpMyAdmin.

==

depends_on: db = phpMyAdmin ne se lance que si MySQL est démarré.

==

environment = Indique où se trouve le serveur MySQL (PMA_HOST: db) et le mot de passe root.

==

ports: "8081:80" = Accessible sur http://localhost:8081.

==

web = Serveur PHP/Apache pour exécuter ton code = http://localhost:8080

==

db = Serveur MySQL = Port 3306, nom db dans le réseau Docker

==

phpmyadmin = Interface graphique pour gérer MySQL = http://localhost:8081

========================================

version: '3.9'

services:
  web:
    build: .
    container_name: php-apache
    ports:
      - "8080:80"   # Apache accessible sur localhost:8080
    volumes:
      - .:/var/www/html:cached   # Le "cached" accélère la lecture sur Mac/Windows
    depends_on:
      - db
    restart: unless-stopped

  db:
    image: mysql:8
    container_name: mysql-db
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: ma_base
      MYSQL_USER: utilisateur
      MYSQL_PASSWORD: motdepasse
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin
    restart: unless-stopped
    environment:
      PMA_HOST: db
      PMA_PORT: 3306
    ports:
      - "8081:80"   # Port changé pour éviter conflit
    depends_on:
      - db

volumes:
  db_data:


========================================

FROM php:8.2-apache

# Installer extensions PHP utiles
RUN docker-php-ext-install mysqli pdo pdo_mysql

# Activer mod_rewrite
RUN a2enmod rewrite

# Définir le bon DocumentRoot
ENV APACHE_DOCUMENT_ROOT=/var/www/html/public

# Appliquer le changement de DocumentRoot dans Apache
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/000-default.conf

# Copier tout le contenu du projet dans le conteneur
COPY . /var/www/html/

========================================
