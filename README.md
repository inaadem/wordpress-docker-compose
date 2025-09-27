# WordPress Docker Setup Guide# WordPress Docker Setup Guide



Complete guide for deploying WordPress with Docker Compose, SSL certificates, and production-ready configuration.Complete guide for deploying WordPress with Docker Compose, SSL certificates, and production-ready configuration.



## Quick Overview## Quick Overview



This tutorial walks you through creating a containerized WordPress installation with:This tutorial walks you through creating a containerized WordPress installation with:

- Nginx web server- Nginx web server

- MySQL database- MySQL database

- Automatic SSL certificates- Automatic SSL certificates

- Production security- Production security



## What You Need## What You Need



- Ubuntu server (22.04/20.04)- Ubuntu server (22.04/20.04)

- Docker & Docker Compose- Docker & Docker Compose

- Domain name- Domain name

- Basic command line knowledge- Basic command line knowledge



## Architecture## Architecture



```text```text

ServerServer

├── Nginx (Port 80/443) - Web server├── Nginx (Port 80/443) - Web server

├── WordPress (Port 9000) - CMS├── WordPress (Port 9000) - CMS

├── MySQL (Port 3306) - Database├── MySQL (Port 3306) - Database

└── Certbot - SSL certificates└── Certbot - SSL certificates

``````



## Step 1: Web Server Configuration## Step 1: Web Server Configuration



Create Nginx configuration:Create Nginx configuration:



```bash```bash

mkdir wordpress-docker-composemkdir wordpress-docker-compose

cd wordpress-docker-composecd wordpress-docker-compose

mkdir nginx-confmkdir nginx-conf

nano nginx-conf/default.confnano nginx-conf/default.conf

``````



Add this configuration (replace `your_domain`):Add this configuration (replace `your_domain`):



```nginx```nginx

server {server {

    listen 80;    listen 80;

    server_name your_domain www.your_domain;    server_name your_domain www.your_domain;

        

    location ~ /.well-known/acme-challenge {    location ~ /.well-known/acme-challenge {

        allow all;        allow all;

        root /var/www/html;        root /var/www/html;

    }    }

        

    location / {    location / {

        try_files $uri $uri/ /index.php$is_args$args;        try_files $uri $uri/ /index.php$is_args$args;

    }    }

        

    location ~ \.php$ {    location ~ \.php$ {

        fastcgi_pass wordpress:9000;        fastcgi_pass wordpress:9000;

        fastcgi_index index.php;        fastcgi_index index.php;

        include fastcgi_params;        include fastcgi_params;

        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

    }    }

}}

``````



## Step 2: Environment Variables## Step 2: Environment Variables



Create `.env` file:Create `.env` file:



```bash```bash

nano .envnano .env

``````



Add database credentials:Add database credentials:



```bash```bash

MYSQL_ROOT_PASSWORD=your_strong_passwordMYSQL_ROOT_PASSWORD=your_strong_password

MYSQL_USER=wordpress_userMYSQL_USER=wordpress_user

MYSQL_PASSWORD=your_wp_passwordMYSQL_PASSWORD=your_wp_password

``````



## Step 3: Docker Compose Configuration## Step 3: Docker Compose Configuration



Create `docker-compose.yml`:Create `docker-compose.yml`:



```bash```bash

nano docker-compose.ymlnano docker-compose.yml

``````



Add service definitions:Add service definitions:



```yaml```yaml

version: '3'version: '3'



services:services:

  db:  db:

    image: mysql:8.0    image: mysql:8.0

    container_name: db    container_name: db

    restart: unless-stopped    restart: unless-stopped

    env_file: .env    env_file: .env

    environment:    environment:

      - MYSQL_DATABASE=wordpress      - MYSQL_DATABASE=wordpress

      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}

      - MYSQL_USER=${MYSQL_USER}      - MYSQL_USER=${MYSQL_USER}

      - MYSQL_PASSWORD=${MYSQL_PASSWORD}      - MYSQL_PASSWORD=${MYSQL_PASSWORD}

    volumes:    volumes:

      - dbdata:/var/lib/mysql      - dbdata:/var/lib/mysql

    command: '--default-authentication-plugin=mysql_native_password'    command: '--default-authentication-plugin=mysql_native_password'

    networks:    networks:

      - app-network      - app-network



  wordpress:  wordpress:

    depends_on:    depends_on:

      - db      - db

    image: wordpress:5.1.1-fpm-alpine    image: wordpress:5.1.1-fpm-alpine

    container_name: wordpress    container_name: wordpress

    restart: unless-stopped    restart: unless-stopped

    env_file: .env    env_file: .env

    environment:    environment:

      - WORDPRESS_DB_HOST=db:3306      - WORDPRESS_DB_HOST=db:3306

      - WORDPRESS_DB_USER=${MYSQL_USER}      - WORDPRESS_DB_USER=${MYSQL_USER}

      - WORDPRESS_DB_PASSWORD=${MYSQL_PASSWORD}      - WORDPRESS_DB_PASSWORD=${MYSQL_PASSWORD}

      - WORDPRESS_DB_NAME=wordpress      - WORDPRESS_DB_NAME=wordpress

    volumes:    volumes:

      - wordpress:/var/www/html      - wordpress:/var/www/html

    networks:    networks:

      - app-network      - app-network



  webserver:  webserver:

    depends_on:    depends_on:

      - wordpress      - wordpress

    image: nginx:1.15.12-alpine    image: nginx:1.15.12-alpine

    container_name: webserver    container_name: webserver

    restart: unless-stopped    restart: unless-stopped

    ports:    ports:

      - "80:80"      - "80:80"

      - "443:443"      - "443:443"

    volumes:    volumes:

      - wordpress:/var/www/html      - wordpress:/var/www/html

      - ./nginx-conf:/etc/nginx/conf.d      - ./nginx-conf:/etc/nginx/conf.d

      - certbot-etc:/etc/letsencrypt      - certbot-etc:/etc/letsencrypt

    networks:    networks:

      - app-network      - app-network



  certbot:  certbot:

    depends_on:    depends_on:

      - webserver      - webserver

    image: certbot/certbot    image: certbot/certbot

    container_name: certbot    container_name: certbot

    volumes:    volumes:

      - certbot-etc:/etc/letsencrypt      - certbot-etc:/etc/letsencrypt

      - wordpress:/var/www/html      - wordpress:/var/www/html

    command: certonly --webroot --webroot-path=/var/www/html --email your-email@domain.com --agree-tos --no-eff-email --staging -d your_domain -d www.your_domain    command: certonly --webroot --webroot-path=/var/www/html --email your-email@domain.com --agree-tos --no-eff-email --staging -d your_domain -d www.your_domain



volumes:volumes:

  certbot-etc:  certbot-etc:

  wordpress:  wordpress:

  dbdata:  dbdata:



networks:networks:

  app-network:  app-network:

    driver: bridge    driver: bridge

``````



## Step 4: Start Services## Step 4: Start Services



Launch containers:Launch containers:



```bash```bash

docker-compose up -ddocker-compose up -d

``````



Check status:Check status:



```bash```bash

docker-compose psdocker-compose ps

``````



## Step 5: SSL Configuration## Step 5: SSL Configuration



After staging certificates work, update for production:After staging certificates work, update for production:



Edit `docker-compose.yml` - remove `--staging`, add `--force-renewal`:Edit `docker-compose.yml` - remove `--staging`, add `--force-renewal`:



```bash```bash

docker-compose up --force-recreate --no-deps certbotdocker-compose up --force-recreate --no-deps certbot

``````



Update Nginx for SSL:Update Nginx for SSL:



```nginx```nginx

server {server {

    listen 443 ssl http2;    listen 443 ssl http2;

    server_name your_domain www.your_domain;    server_name your_domain www.your_domain;

        

    ssl_certificate /etc/letsencrypt/live/your_domain/fullchain.pem;    ssl_certificate /etc/letsencrypt/live/your_domain/fullchain.pem;

    ssl_certificate_key /etc/letsencrypt/live/your_domain/privkey.pem;    ssl_certificate_key /etc/letsencrypt/live/your_domain/privkey.pem;

        

    location / {    location / {

        try_files $uri $uri/ /index.php$is_args$args;        try_files $uri $uri/ /index.php$is_args$args;

    }    }

        

    location ~ \.php$ {    location ~ \.php$ {

        fastcgi_pass wordpress:9000;        fastcgi_pass wordpress:9000;

        fastcgi_index index.php;        fastcgi_index index.php;

        include fastcgi_params;        include fastcgi_params;

        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

    }    }

}}

``````



## Access Your Site## Access Your Site



Visit: `https://your_domain`Visit: `https://your_domain`



Complete WordPress setup wizard.Complete WordPress setup wizard.



## Quick Commands## Quick Commands



```bash```bash

# View logs# View logs

docker-compose logsdocker-compose logs



# Restart service# Restart service

docker-compose restart webserverdocker-compose restart webserver



# Database backup# Database backup

docker-compose exec db mysqldump -u root -p wordpress > backup.sqldocker-compose exec db mysqldump -u root -p wordpress > backup.sql



# Stop all# Stop all

docker-compose downdocker-compose down

``````



## License## License



MIT LicenseMIT License
