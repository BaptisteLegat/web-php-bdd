Pour pouvoir initialiser un projet avec docker, il faut créer un fichier docker-compose.yaml à la racine du projet.
Dans ce projet, nous avons un fichier SQL qui permet d'initialiser la base de données. De plus, nous avons un fichier PHP qui permet de créer un site web.
Le docker-compose.yaml permet de lancer un serveur web, un serveur de base de données et un serveur nginx.
Le docker-compose.yaml :

```
version: '3.8'

services:
    web:
        image: arm64v8/php:7.4-fpm
        container_name: php
        volumes:
            - ./php:/var/www/html  # Mounts the local ./php directory to the /var/www/html directory inside the container
        depends_on:
            - db

    nginx:
        image: arm64v8/nginx:latest
        container_name: nginx
        ports:
            - "8080:80"  # Maps port 8080 on the host to port 80 on the container
        volumes:
            - ./nginx/default.conf:/etc/nginx/conf.d/default.conf  # Mounts the local ./nginx/default.conf file to the /etc/nginx/conf.d/default.conf file inside the container
            - ./php:/var/www/html  # Mounts the local ./php directory to the /var/www/html directory inside the container
        depends_on:
            - web

    db:
        image: arm64v8/mariadb:latest
        container_name: mariadb
        environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_DATABASE: mydatabase
            MYSQL_USER: user
            MYSQL_PASSWORD: password
        volumes:
            - ./mysql/initDB.sql:/docker-entrypoint-initdb.d/initDB.sql  # Mounts the local ./mysql/initDB.sql file to the /docker-entrypoint-initdb.d/initDB.sql file inside the container
```

Pour faire fonctionner la db, il faut : 

- Accédez à votre conteneur PHP :

Utilisez la commande docker-compose exec pour accéder à une session shell interactive dans votre conteneur PHP :

    docker-compose exec web bash

- Installer pdo_mysql :

À l'intérieur du conteneur PHP, installez l'extension pdo_mysql :

    docker-php-ext-install pdo_mysql

Assurez-vous de suivre les instructions interactives pour terminer l'installation de l'extension.

- Redémarrez le conteneur PHP :

Après avoir installé pdo_mysql, redémarrez le conteneur PHP pour appliquer les modifications :

    exit  # Quitter le shell du conteneur PHP
    docker-compose restart web