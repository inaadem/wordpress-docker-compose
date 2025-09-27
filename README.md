# WordPress Docker Setup Guide

Complete guide for deploying WordPress with Docker Compose, SSL certificates, and production-ready configuration.

## Quick Overview

This tutorial walks you through creating a containerized WordPress installation with:
- Nginx web server
- MySQL database
- Automatic SSL certificates
- Production security

## What You Need

- Ubuntu server (22.04/20.04)
- Docker & Docker Compose
- Domain name
- Basic command line knowledge

## Architecture

```text
Server
├── Nginx (Port 80/443) - Web server
├── WordPress (Port 9000) - CMS
├── MySQL (Port 3306) - Database
└── Certbot - SSL certificates
```

## Step 1: Web Server Configuration

Create Nginx configuration:

```bash
mkdir wordpress-docker-compose
cd wordpress-docker-compose
mkdir nginx-conf
nano nginx-conf/default.conf
```

Add this configuration (replace `your_domain`):

```nginx
server {
    listen 80;
    server_name your_domain www.your_domain;
    
    location ~ /.well-known/acme-challenge {
        allow all;
        root /var/www/html;
    }
    
    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }
    
    location ~ \.php$ {
        fastcgi_pass wordpress:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

## Step 2: Environment Variables

Create `.env` file:

```bash
nano .env
```

Add database credentials:

```bash
MYSQL_ROOT_PASSWORD=your_strong_password
MYSQL_USER=wordpress_user
MYSQL_PASSWORD=your_wp_password
```

## Step 3: Docker Compose Configuration

Create `docker-compose.yml`:

```bash
nano docker-compose.yml
```

Add service definitions:

```yaml
version: '3'

services:
  db:
    image: mysql:8.0
    container_name: db
    restart: unless-stopped
    env_file: .env
    environment:
      - MYSQL_DATABASE=wordpress
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
    volumes:
      - dbdata:/var/lib/mysql
    command: '--default-authentication-plugin=mysql_native_password'
    networks:
      - app-network

  wordpress:
    depends_on:
      - db
    image: wordpress:5.1.1-fpm-alpine
    container_name: wordpress
    restart: unless-stopped
    env_file: .env
    environment:
      - WORDPRESS_DB_HOST=db:3306
      - WORDPRESS_DB_USER=${MYSQL_USER}
      - WORDPRESS_DB_PASSWORD=${MYSQL_PASSWORD}
      - WORDPRESS_DB_NAME=wordpress
    volumes:
      - wordpress:/var/www/html
    networks:
      - app-network

  webserver:
    depends_on:
      - wordpress
    image: nginx:1.15.12-alpine
    container_name: webserver
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - wordpress:/var/www/html
      - ./nginx-conf:/etc/nginx/conf.d
      - certbot-etc:/etc/letsencrypt
    networks:
      - app-network

  certbot:
    depends_on:
      - webserver
    image: certbot/certbot
    container_name: certbot
    volumes:
      - certbot-etc:/etc/letsencrypt
      - wordpress:/var/www/html
    command: certonly --webroot --webroot-path=/var/www/html --email your-email@domain.com --agree-tos --no-eff-email --staging -d your_domain -d www.your_domain

volumes:
  certbot-etc:
  wordpress:
  dbdata:

networks:
  app-network:
    driver: bridge
```

## Step 4: Start Services

Launch containers:

```bash
docker-compose up -d
```

Check status:

```bash
docker-compose ps
```

## Step 5: SSL Configuration

After staging certificates work, update for production:

Edit `docker-compose.yml` - remove `--staging`, add `--force-renewal`:

```bash
docker-compose up --force-recreate --no-deps certbot
```

Update Nginx for SSL:

```nginx
server {
    listen 443 ssl http2;
    server_name your_domain www.your_domain;
    
    ssl_certificate /etc/letsencrypt/live/your_domain/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your_domain/privkey.pem;
    
    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }
    
    location ~ \.php$ {
        fastcgi_pass wordpress:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

## Access Your Site

Visit: `https://your_domain`

Complete WordPress setup wizard.

## Quick Commands

```bash
# View logs
docker-compose logs

# Restart service
docker-compose restart webserver

# Database backup
docker-compose exec db mysqldump -u root -p wordpress > backup.sql

# Stop all
docker-compose down
```

## License

MIT License
