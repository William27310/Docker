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

==