# WordPress on Docker with SSL Certificates

Welcome to the complete guide for deploying WordPress using Docker Compose with automatic SSL certificate management. This tutorial will walk you through setting up a production-ready WordPress installation with Nginx, MySQL, and Let's Encrypt SSL certificates.

## Introduction

This project demonstrates how to containerize WordPress using Docker Compose, creating a scalable and maintainable deployment suitable for production environments. By the end of this guide, you'll have a fully functional WordPress site running with HTTPS encryption.

## Provider Configuration

This setup is designed to work on any cloud provider or VPS service:

- **DigitalOcean** - Droplets with Ubuntu 22.04/20.04
- **AWS EC2** - Ubuntu instances  
- **Google Cloud Platform** - Compute Engine instances
- **Azure** - Virtual Machines
- **Linode** - Cloud instances
- **Vultr** - Cloud compute instances

## VPC Configuration

For cloud deployments, ensure your VPC/network configuration includes:

- **Inbound Rules:**
  - HTTP (Port 80) - For Let's Encrypt validation and HTTP redirect
  - HTTPS (Port 443) - For secure WordPress access
  - SSH (Port 22) - For server management

- **Outbound Rules:**
  - Allow all outbound traffic for package updates and Let's Encrypt

## Prerequisites

Before starting this tutorial, ensure you have:

- ✅ A server running Ubuntu 22.04/20.04 with root or sudo access
- ✅ Docker and Docker Compose installed on your server
- ✅ A registered domain name (e.g., `example.com`)
- ✅ DNS A records pointing your domain to your server's IP address:
  - `example.com` → Your Server IP
  - `www.example.com` → Your Server IP
- ✅ Basic familiarity with command line operations

## Architecture

The application uses Docker containers orchestrated with Docker Compose:

```
┌─────────────────────────────────────────────────────────┐
│                    Server (Ubuntu)                     │
├─────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐  │
│  │   Nginx     │  │ WordPress   │  │     MySQL       │  │
│  │   :80/443   │◄─┤   :9000     │◄─┤     :3306       │  │
│  │             │  │             │  │                 │  │
│  └─────────────┘  └─────────────┘  └─────────────────┘  │
│         ▲                                               │
│         │                                               │
│  ┌─────────────────────────────────────────────────────┐ │
│  │            Let's Encrypt (Certbot)                 │ │
│  │          SSL Certificate Management                │ │
│  └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

**Data Flow:**
1. User requests → Nginx (Port 80/443)
2. Nginx → WordPress container (Port 9000) 
3. WordPress → MySQL database (Port 3306)
4. Certbot → Let's Encrypt for SSL certificates

## Project Structure

```
wordpress-docker-compose/
├── 📄 docker-compose.yml          # Container orchestration
├── 🔒 .env                       # Environment variables (not in git)
├── 📁 nginx-conf/
│   └── 📄 default.conf           # Nginx server configuration
├── 📄 .gitignore                 # Git ignore patterns
├── 📄 .dockerignore              # Docker ignore patterns
├── 📄 README.md                  # This documentation
└── 📁 screenshots/               # Documentation images
    ├── 🖼️ step1-nginx-config.png
    ├── 🖼️ step2-environment.png
    ├── 🖼️ step3-containers.png
    ├── 🖼️ step4-ssl-success.png
    └── 🖼️ wordpress-dashboard.png
```

## Steps to Deploy

### Step 1 — Defining the Web Server Configuration

First, create the project directory and Nginx configuration:

```bash
mkdir wordpress-docker-compose
cd wordpress-docker-compose
mkdir nginx-conf
```

Create the Nginx configuration file:

```bash
nano nginx-conf/default.conf
```

Add the following configuration (replace `your_domain` with your actual domain):

```nginx
server {
    listen 80;
    listen [::]:80;
    
    server_name your_domain www.your_domain;
    
    location ~ /.well-known/acme-challenge {
        allow all;
        root /var/www/html;
    }
    
    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }
    
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass wordpress:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
    
    location ~ /\.ht {
        deny all;
    }
    
    location = /favicon.ico { 
        log_not_found off; access_log off; 
    }
    location = /robots.txt { 
        log_not_found off; access_log off; allow all; 
    }
    location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
        expires max;
        log_not_found off;
    }
}
```

**Screenshot:** `step1-nginx-config.png` - Shows the Nginx configuration file creation

### Step 2 — Defining Environment Variables

Create a secure environment file for database credentials:

```bash
nano .env
```

Add your database configuration:

```bash
MYSQL_ROOT_PASSWORD=your_strong_root_password
MYSQL_USER=wordpress_user  
MYSQL_PASSWORD=your_secure_password
```

Create `.gitignore` to exclude sensitive files:

```bash
nano .gitignore
```

```bash
.env
```

**Screenshot:** `step2-environment.png` - Environment variable configuration

### Step 3 — Defining Services with Docker Compose

Create the main Docker Compose configuration:

```bash
nano docker-compose.yml
```

Add the complete service definitions:

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

Start the containers:

```bash
docker-compose up -d
```

**Screenshot:** `step3-containers.png` - Docker containers running successfully

### Step 4 — Obtaining SSL Certificates and Credentials

Check container status:

```bash
docker-compose ps
```

Verify staging certificates were created:

```bash
docker-compose exec webserver ls -la /etc/letsencrypt/live
```

Switch to production certificates by editing `docker-compose.yml`:
- Remove `--staging` flag
- Add `--force-renewal` flag

Recreate certbot container:

```bash
docker-compose up --force-recreate --no-deps certbot
```

**Screenshot:** `step4-ssl-success.png` - Successful SSL certificate generation

### Step 5 — Modifying the Web Server Configuration

Download recommended SSL configuration:

```bash
curl -sSLo nginx-conf/options-ssl-nginx.conf https://raw.githubusercontent.com/certbot/certbot/master/certbot-nginx/certbot_nginx/_internal/tls_configs/options-ssl-nginx.conf
```

Update Nginx configuration for SSL:

```bash
nano nginx-conf/default.conf
```

Replace with SSL-enabled configuration:

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name your_domain www.your_domain;
    
    location ~ /.well-known/acme-challenge {
        allow all;
        root /var/www/html;
    }
    
    location / {
        rewrite ^ https://$host$request_uri? permanent;
    }
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name your_domain www.your_domain;
    
    index index.php index.html index.htm;
    root /var/www/html;
    
    server_tokens off;
    
    ssl_certificate /etc/letsencrypt/live/your_domain/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your_domain/privkey.pem;
    
    include /etc/nginx/conf.d/options-ssl-nginx.conf;
    
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    add_header Content-Security-Policy "default-src * data: 'unsafe-eval' 'unsafe-inline'" always;
    
    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }
    
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass wordpress:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
    
    location ~ /\.ht {
        deny all;
    }
    
    location = /favicon.ico { 
        log_not_found off; access_log off; 
    }
    location = /robots.txt { 
        log_not_found off; access_log off; allow all; 
    }
    location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
        expires max;
        log_not_found off;
    }
}
```

Restart the web server:

```bash
docker-compose restart webserver
```

## WordPress Installation

1. Visit your domain: `https://your_domain`
2. Complete WordPress installation wizard
3. Access admin dashboard at: `https://your_domain/wp-admin`

**Screenshot:** `wordpress-dashboard.png` - WordPress admin dashboard

## Container Status & Monitoring

Check all containers are running:

```bash
docker-compose ps
```

Expected output:
```
NAME        COMMAND                  STATUS          PORTS
certbot     "certbot certonly --w…"  Exited (0)      
db          "docker-entrypoint.s…"   Up              3306/tcp
webserver   "nginx -g 'daemon of…'"  Up              0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp
wordpress   "docker-entrypoint.s…"   Up              9000/tcp
```

## SSL Certificate Auto-Renewal

Create renewal script:

```bash
nano ssl_renew.sh
```

```bash
#!/bin/bash
cd /path/to/wordpress-docker-compose
docker-compose run --rm certbot renew
docker-compose exec webserver nginx -s reload
```

Make executable and add to crontab:

```bash
chmod +x ssl_renew.sh
crontab -e

# Add this line for daily renewal check at 2 AM
0 2 * * * /path/to/wordpress-docker-compose/ssl_renew.sh
```

## Troubleshooting

### Check logs:
```bash
docker-compose logs [service_name]
```

### Restart services:
```bash
docker-compose restart [service_name]
```

### Database backup:
```bash
docker-compose exec db mysqldump -u root -p${MYSQL_ROOT_PASSWORD} wordpress > backup.sql
```

## License

MIT License - Free to use and modify.