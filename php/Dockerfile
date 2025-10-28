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
