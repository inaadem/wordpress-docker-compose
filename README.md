# WordPress on Docker with SSL Certificates# WordPress Docker Compose# WordPress Docker Compose# WordPress Docker Compose



Welcome to the complete guide for deploying WordPress using Docker Compose with automatic SSL certificate management. This tutorial will walk you through setting up a production-ready WordPress installation with Nginx, MySQL, and Let's Encrypt SSL certificates.



## IntroductionA complete WordPress installation using Docker Compose with a LEMP stack (Linux, Nginx, MySQL, and PHP). This setup includes automatic SSL certificate generation and renewal with Let's Encrypt.



This project demonstrates how to containerize WordPress using Docker Compose, creating a scalable and maintainable deployment suitable for production environments. By the end of this guide, you'll have a fully functional WordPress site running with HTTPS encryption.



## Provider Configuration## OverviewA production-ready WordPress deployment using Docker Compose with Nginx, MySQL, and automatic SSL certificates.A production-ready WordPress deployment using Docker Compose with Nginx, MySQL, and automatic SSL certificates.



This setup is designed to work on any cloud provider or VPS service:



- **DigitalOcean** - Droplets with Ubuntu 22.04/20.04WordPress is a popular Content Management System (CMS) that typically requires a LAMP or LEMP stack installation. By using Docker and Docker Compose, we can streamline this process by using pre-configured images and containers that work together seamlessly.

- **AWS EC2** - Ubuntu instances  

- **Google Cloud Platform** - Compute Engine instances

- **Azure** - Virtual Machines

- **Linode** - Cloud instancesThis setup uses standardized Docker images for consistent deployments and includes SSL/TLS encryption for production security.## Features## Features

- **Vultr** - Cloud compute instances



## VPC Configuration

## Stack Components

For cloud deployments, ensure your VPC/network configuration includes:



- **Inbound Rules:**

  - HTTP (Port 80) - For Let's Encrypt validation and HTTP redirect- **WordPress 5.1.1-fpm-alpine** - Content Management System with PHP-FPM processor- **WordPress 5.1.1** with PHP-FPM- **WordPress 5.1.1** with PHP-FMP

  - HTTPS (Port 443) - For secure WordPress access

  - SSH (Port 22) - For server management- **MySQL 8.0** - Database server with native password authentication  



- **Outbound Rules:**- **Nginx 1.15.12-alpine** - Web server and reverse proxy- **MySQL 8.0** database  - **MySQL 8.0** database

  - Allow all outbound traffic for package updates and Let's Encrypt

- **Certbot** - Automated SSL certificate management via Let's Encrypt

## Prerequisites

- **Nginx** web server with SSL support- **Nginx** web server with SSL support

Before starting this tutorial, ensure you have:

## Architecture

- ✅ A server running Ubuntu 22.04/20.04 with root or sudo access

- ✅ Docker and Docker Compose installed on your server- **Let's Encrypt** automatic SSL certificates- **Let's Encrypt** automatic SSL certificates

- ✅ A registered domain name (e.g., `example.com`)

- ✅ DNS A records pointing your domain to your server's IP address:The application uses Docker's bridge networking to enable communication between containers while only exposing necessary ports to the host system.

  - `example.com` → Your Server IP

  - `www.example.com` → Your Server IP- **Docker Compose** orchestration- **Docker Compose** orchestration

- ✅ Basic familiarity with command line operations

```

## Architecture

┌─────────────────────────────────────────────┐- Production-ready configuration- Production-ready configuration

The application uses Docker containers orchestrated with Docker Compose:

│                   Server                    │

```

┌─────────────────────────────────────────────────────────┐├─────────────────────────────────────────────┤

│                    Server (Ubuntu)                     │

├─────────────────────────────────────────────────────────┤│  ┌─────────────┐  ┌─────────────┐  ┌───────┐ │

│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐  │

│  │   Nginx     │  │ WordPress   │  │     MySQL       │  ││  │   Nginx     │  │ WordPress   │  │ MySQL │ │## Architecture## Architecture

│  │   :80/443   │◄─┤   :9000     │◄─┤     :3306       │  │

│  │             │  │             │  │                 │  ││  │   :80/443   │  │   :9000     │  │ :3306 │ │

│  └─────────────┘  └─────────────┘  └─────────────────┘  │

│         ▲                                               ││  └─────┬───────┘  └─────┬───────┘  └───┬───┘ │

│         │                                               │

│  ┌─────────────────────────────────────────────────────┐ ││        │                │              │     │

│  │            Let's Encrypt (Certbot)                 │ │

│  │          SSL Certificate Management                │ ││        └────────────────┼──────────────┘     │``````

│  └─────────────────────────────────────────────────────┘ │

└─────────────────────────────────────────────────────────┘│                         │                    │

```

│  ┌─────────────────────────────────────────┐ │┌─────────────────────────────────────────────┐┌─────────────────────────────────────────────┐

**Data Flow:**

1. User requests → Nginx (Port 80/443)│  │         Let's Encrypt (Certbot)        │ │

2. Nginx → WordPress container (Port 9000) 

3. WordPress → MySQL database (Port 3306)│  └─────────────────────────────────────────┘ ││                   Server                    ││                   Server                    │

4. Certbot → Let's Encrypt for SSL certificates

└─────────────────────────────────────────────┘

## Project Structure

```├─────────────────────────────────────────────┤├─────────────────────────────────────────────┤

```

wordpress-docker-compose/

├── 📄 docker-compose.yml          # Container orchestration

├── 🔒 .env                       # Environment variables (not in git)## Project Structure│  ┌─────────────┐  ┌─────────────┐  ┌───────┐ ││  ┌─────────────┐  ┌─────────────┐  ┌───────┐ │

├── 📁 nginx-conf/

│   └── 📄 default.conf           # Nginx server configuration

├── 📄 .gitignore                 # Git ignore patterns

├── 📄 .dockerignore              # Docker ignore patterns```│  │   Nginx     │  │ WordPress   │  │ MySQL │ ││  │   Nginx     │  │ WordPress   │  │ MySQL │ │

├── 📄 README.md                  # This documentation

└── 📁 screenshots/               # Documentation imageswordpress-docker-compose/

    ├── 🖼️ step1-nginx-config.png

    ├── 🖼️ step2-environment.png├── docker-compose.yml     # Service definitions and container orchestration│  │ (Webserver) │  │   (PHP)     │  │  (DB) │ ││  │ (Webserver) │  │   (PHP)     │  │  (DB) │ │

    ├── 🖼️ step3-containers.png

    ├── 🖼️ step4-ssl-success.png├── .env                  # Environment variables (not tracked in git)

    └── 🖼️ wordpress-dashboard.png

```├── nginx-conf/│  │  Port 80    │  │ Port 9000   │  │ 3306  │ ││  │  Port 80    │  │ Port 9000   │  │ 3306  │ │



## Steps to Deploy│   └── default.conf     # Nginx server configuration



### Step 1 — Defining the Web Server Configuration├── .gitignore           # Git ignore patterns│  │  Port 443   │  │             │  │       │ ││  │  Port 443   │  │             │  │       │ │



First, create the project directory and Nginx configuration:├── .dockerignore        # Docker ignore patterns



```bash└── README.md            # This documentation│  └─────────────┘  └─────────────┘  └───────┘ ││  └─────────────┘  └─────────────┘  └───────┘ │

mkdir wordpress-docker-compose

cd wordpress-docker-compose```

mkdir nginx-conf

```│  ┌─────────────────────────────────────────┐ ││  ┌─────────────────────────────────────────┐ │



Create the Nginx configuration file:## Prerequisites



```bash│  │         Let's Encrypt (Certbot)        │ ││  │         Let's Encrypt (Certbot)        │ │

nano nginx-conf/default.conf

```- Docker and Docker Compose installed on your server



Add the following configuration (replace `your_domain` with your actual domain):- A registered domain name pointing to your server's IP address│  └─────────────────────────────────────────┘ ││  └─────────────────────────────────────────┘ │



```nginx- Basic familiarity with command line operations

server {

    listen 80;└─────────────────────────────────────────────┘└─────────────────────────────────────────────┘

    listen [::]:80;

    ## Installation

    server_name your_domain www.your_domain;

    ``````

    location ~ /.well-known/acme-challenge {

        allow all;### 1. Clone Repository

        root /var/www/html;

    }

    

    location / {```bash

        try_files $uri $uri/ /index.php$is_args$args;

    }git clone https://github.com/inaadem/wordpress-docker-compose.git## Project Structure## Project Structure

    

    location ~ \.php$ {cd wordpress-docker-compose

        try_files $uri =404;

        fastcgi_split_path_info ^(.+\.php)(/.+)$;```

        fastcgi_pass wordpress:9000;

        fastcgi_index index.php;

        include fastcgi_params;

        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;### 2. Configure Environment Variables``````

        fastcgi_param PATH_INFO $fastcgi_path_info;

    }

    

    location ~ /\.ht {Create a `.env` file with your database credentials:wordpress-docker-compose/wordpress-docker-compose/

        deny all;

    }

    

    location = /favicon.ico { ```bash├── docker-compose.yml     # Main orchestration file├── docker-compose.yml     # Main orchestration file

        log_not_found off; access_log off; 

    }MYSQL_ROOT_PASSWORD=secure_root_password

    location = /robots.txt { 

        log_not_found off; access_log off; allow all; MYSQL_USER=wordpress_user├── .env                  # Environment variables (not tracked)├── .env                  # Environment variables (not tracked)

    }

    location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {MYSQL_PASSWORD=secure_user_password

        expires max;

        log_not_found off;```├── nginx-conf/├── nginx-conf/

    }

}

```

**Security Note:** Use strong, unique passwords for production deployments.│   └── default.conf     # Nginx configuration│   └── default.conf     # Nginx configuration

**Screenshot:** `step1-nginx-config.png` - Shows the Nginx configuration file creation



### Step 2 — Defining Environment Variables

### 3. Update Domain Configuration├── .gitignore           # Git ignore rules├── .gitignore           # Git ignore rules

Create a secure environment file for database credentials:



```bash

nano .envEdit the following files to replace placeholder domains with your actual domain:├── .dockerignore        # Docker ignore rules├── .dockerignore        # Docker ignore rules

```



Add your database configuration:

**docker-compose.yml:**└── README.md            # This file└── README.md            # This file

```bash

MYSQL_ROOT_PASSWORD=your_strong_root_password- Replace `your-email@domain.com` with your email address

MYSQL_USER=wordpress_user  

MYSQL_PASSWORD=your_secure_password- Replace `yourdomain.com` with your domain``````

```



Create `.gitignore` to exclude sensitive files:

**nginx-conf/default.conf:**

```bash

nano .gitignore- Replace `yourdomain.com` with your domain in server_name directive

```

## Quick Start## Quick Start

```bash

.env### 4. Initial SSL Certificate (Staging)

```



**Screenshot:** `step2-environment.png` - Environment variable configuration

For first-time setup, the configuration uses Let's Encrypt's staging environment to avoid rate limits:

### Step 3 — Defining Services with Docker Compose

### Prerequisites### Prerequisites

Create the main Docker Compose configuration:

```bash

```bash

nano docker-compose.ymldocker-compose up -d- Docker and Docker Compose installed

```

```

Add the complete service definitions:

- Docker and Docker Compose installed- Domain name (optional for local testing)

```yaml

version: '3'### 5. Verify Certificate Generation



services:- Domain name (optional for local testing)

  db:

    image: mysql:8.0Check that staging certificates were created successfully:

    container_name: db

    restart: unless-stopped### 1. Clone Repository

    env_file: .env

    environment:```bash

      - MYSQL_DATABASE=wordpress

      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}docker-compose exec webserver ls -la /etc/letsencrypt/live### 1. Clone Repository```bash

      - MYSQL_USER=${MYSQL_USER}

      - MYSQL_PASSWORD=${MYSQL_PASSWORD}```

    volumes:

      - dbdata:/var/lib/mysqlgit clone https://github.com/inaadem/wordpress-docker-compose.git

    command: '--default-authentication-plugin=mysql_native_password'

    networks:### 6. Switch to Production Certificates

      - app-network

```bashcd wordpress-docker-compose

  wordpress:

    depends_on:Once staging certificates work, update docker-compose.yml:

      - db

    image: wordpress:5.1.1-fpm-alpine- Remove `--staging` flag from certbot commandgit clone https://github.com/inaadem/wordpress-docker-compose.git```

    container_name: wordpress

    restart: unless-stopped- Add `--force-renewal` flag

    env_file: .env

    environment:cd wordpress-docker-compose

      - WORDPRESS_DB_HOST=db:3306

      - WORDPRESS_DB_USER=${MYSQL_USER}Then recreate the certbot container:

      - WORDPRESS_DB_PASSWORD=${MYSQL_PASSWORD}

      - WORDPRESS_DB_NAME=wordpress```### 2. Configure Environment

    volumes:

      - wordpress:/var/www/html```bash

    networks:

      - app-networkdocker-compose up --force-recreate --no-deps certbot```bash



  webserver:```

    depends_on:

      - wordpress### 2. Configure Environment# Create environment file

    image: nginx:1.15.12-alpine

    container_name: webserver## Access Your Site

    restart: unless-stopped

    ports:cp .env.example .env

      - "80:80"

      - "443:443"- **HTTP:** `http://yourdomain.com` (redirects to HTTPS)

    volumes:

      - wordpress:/var/www/html- **HTTPS:** `https://yourdomain.com`Create a `.env` file with your database credentials:

      - ./nginx-conf:/etc/nginx/conf.d

      - certbot-etc:/etc/letsencrypt- **WordPress Admin:** `https://yourdomain.com/wp-admin`

    networks:

      - app-network# Edit .env with your database credentials



  certbot:## Configuration Details

    depends_on:

      - webserver```bashMYSQL_ROOT_PASSWORD=your_strong_password

    image: certbot/certbot

    container_name: certbot### Environment Variables

    volumes:

      - certbot-etc:/etc/letsencryptMYSQL_ROOT_PASSWORD=your_strong_passwordMYSQL_USER=wpuser

      - wordpress:/var/www/html

    command: certonly --webroot --webroot-path=/var/www/html --email your-email@domain.com --agree-tos --no-eff-email --staging -d your_domain -d www.your_domain| Variable | Purpose | Example |



volumes:|----------|---------|---------|MYSQL_USER=wpuserMYSQL_PASSWORD=your_wp_password

  certbot-etc:

  wordpress:| `MYSQL_ROOT_PASSWORD` | MySQL root account password | `secure_root_pass_123` |

  dbdata:

| `MYSQL_USER` | WordPress database username | `wp_user` |MYSQL_PASSWORD=your_wp_password```

networks:

  app-network:| `MYSQL_PASSWORD` | WordPress database password | `secure_wp_pass_123` |

    driver: bridge

``````



Start the containers:### Container Communication



```bash### 3. Update Domain Configuration

docker-compose up -d

```- **Database:** WordPress connects to MySQL via internal Docker network on port 3306



**Screenshot:** `step3-containers.png` - Docker containers running successfully- **Web Server:** Nginx proxies PHP requests to WordPress container on port 9000### 3. Update Domain ConfigurationEdit `docker-compose.yml` and `nginx-conf/default.conf`:



### Step 4 — Obtaining SSL Certificates and Credentials- **SSL Certificates:** Shared volume between Nginx and Certbot containers



Check container status:- Replace `yourdomain.com` with your actual domain



```bash### Security Features

docker-compose ps

```Edit `docker-compose.yml` and `nginx-conf/default.conf`:- Replace `your-email@domain.com` with your email



Verify staging certificates were created:- **SSL/TLS Encryption:** Automatic HTTPS with Let's Encrypt certificates



```bash- **Security Headers:** X-Frame-Options, X-XSS-Protection, Content Security Policy

docker-compose exec webserver ls -la /etc/letsencrypt/live

```- **MySQL Authentication:** Uses native password plugin for compatibility



Switch to production certificates by editing `docker-compose.yml`:- **Network Isolation:** Containers communicate via private Docker network- Replace `yourdomain.com` with your actual domain### 4. Start Services

- Remove `--staging` flag

- Add `--force-renewal` flag



Recreate certbot container:## Management Commands- Replace `your-email@domain.com` with your email```bash



```bash

docker-compose up --force-recreate --no-deps certbot

```### Container Operations# Start all containers



**Screenshot:** `step4-ssl-success.png` - Successful SSL certificate generation



### Step 5 — Modifying the Web Server Configuration```bash### 4. Start Servicesdocker-compose up -d



Download recommended SSL configuration:# Start all services



```bashdocker-compose up -d

curl -sSLo nginx-conf/options-ssl-nginx.conf https://raw.githubusercontent.com/certbot/certbot/master/certbot-nginx/certbot_nginx/_internal/tls_configs/options-ssl-nginx.conf

```



Update Nginx configuration for SSL:# Stop all services  ```bash# Check status



```bashdocker-compose down

nano nginx-conf/default.conf

```# Start all containersdocker-compose ps



Replace with SSL-enabled configuration:# View container status



```nginxdocker-compose psdocker-compose up -d

server {

    listen 80;

    listen [::]:80;

    server_name your_domain www.your_domain;# View logs# View logs

    

    location ~ /.well-known/acme-challenge {docker-compose logs

        allow all;

        root /var/www/html;# Check statusdocker-compose logs

    }

    # Update images and restart

    location / {

        rewrite ^ https://$host$request_uri? permanent;docker-compose pull && docker-compose up -d --force-recreatedocker-compose ps```

    }

}```



server {

    listen 443 ssl http2;

    listen [::]:443 ssl http2;### Database Management

    server_name your_domain www.your_domain;

    # View logs### 5. Access WordPress

    index index.php index.html index.htm;

    root /var/www/html;```bash

    

    server_tokens off;# Create database backupdocker-compose logs- **Local:** http://localhost

    

    ssl_certificate /etc/letsencrypt/live/your_domain/fullchain.pem;docker-compose exec db mysqldump -u root -p${MYSQL_ROOT_PASSWORD} wordpress > backup.sql

    ssl_certificate_key /etc/letsencrypt/live/your_domain/privkey.pem;

    ```- **Production:** https://yourdomain.com

    include /etc/nginx/conf.d/options-ssl-nginx.conf;

    # Restore from backup

    add_header X-Frame-Options "SAMEORIGIN" always;

    add_header X-XSS-Protection "1; mode=block" always;docker-compose exec -T db mysql -u root -p${MYSQL_ROOT_PASSWORD} wordpress < backup.sql

    add_header X-Content-Type-Options "nosniff" always;

    add_header Referrer-Policy "no-referrer-when-downgrade" always;```

    add_header Content-Security-Policy "default-src * data: 'unsafe-eval' 'unsafe-inline'" always;

    ### 5. Access WordPress## 📸 Screenshots & Explanations

    location / {

        try_files $uri $uri/ /index.php$is_args$args;### SSL Certificate Management

    }

    

    location ~ \.php$ {

        try_files $uri =404;```bash

        fastcgi_split_path_info ^(.+\.php)(/.+)$;

        fastcgi_pass wordpress:9000;# Test certificate renewal (dry run)- **Local:** <http://localhost>### 1. Container Status

        fastcgi_index index.php;

        include fastcgi_params;docker-compose run --rm certbot renew --dry-run

        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

        fastcgi_param PATH_INFO $fastcgi_path_info;- **Production:** <https://yourdomain.com>**ADD SCREENSHOT HERE: `docker-compose ps` output**

    }

    # Force certificate renewal

    location ~ /\.ht {

        deny all;docker-compose run --rm certbot renew --force-renewal```bash

    }

    

    location = /favicon.ico { 

        log_not_found off; access_log off; # Check certificate expiration## ConfigurationNAME        IMAGE                        COMMAND                  SERVICE     STATUS          PORTS

    }

    location = /robots.txt { docker-compose exec webserver openssl x509 -noout -dates -in /etc/letsencrypt/live/yourdomain.com/cert.pem

        log_not_found off; access_log off; allow all; 

    }```db          mysql:8.0                    "docker-entrypoint.s…"   db          Up 2 hours      3306/tcp, 33060/tcp

    location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {

        expires max;

        log_not_found off;

    }## Automatic Certificate Renewal### Environment Variables (.env)webserver   nginx:1.15.12-alpine         "nginx -g 'daemon of…"   webserver   Up 56 seconds   0.0.0.0:80->80/tcp, [::]:80->80/tcp

}

```



Restart the web server:To set up automatic certificate renewal, create a cron job:wordpress   wordpress:5.1.1-fpm-alpine   "docker-entrypoint.s…"   wordpress   Up 2 hours      9000/tcp



```bash

docker-compose restart webserver

``````bash```bash```



## WordPress Installation# Edit crontab



1. Visit your domain: `https://your_domain`crontab -eMYSQL_ROOT_PASSWORD=strongrootpassword123**Explanation:** Shows all three containers running successfully - database, webserver, and WordPress application.

2. Complete WordPress installation wizard

3. Access admin dashboard at: `https://your_domain/wp-admin`



**Screenshot:** `wordpress-dashboard.png` - WordPress admin dashboard# Add this line to renew certificates daily at 2 AMMYSQL_USER=wpuser



## Container Status & Monitoring0 2 * * * cd /path/to/wordpress-docker-compose && docker-compose run --rm certbot renew && docker-compose exec webserver nginx -s reload



Check all containers are running:```MYSQL_PASSWORD=wppassword123### 2. WordPress Installation Screen



```bash

docker-compose ps

```## Troubleshooting```**ADD SCREENSHOT HERE: Initial WordPress setup page**



Expected output:

```

NAME        COMMAND                  STATUS          PORTS### Container Issues- Language selection

certbot     "certbot certonly --w…"  Exited (0)      

db          "docker-entrypoint.s…"   Up              3306/tcp

webserver   "nginx -g 'daemon of…'"  Up              0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp

wordpress   "docker-entrypoint.s…"   Up              9000/tcp```bash### SSL Certificate Setup- Database configuration confirmation

```

# Check container logs

## SSL Certificate Auto-Renewal

docker-compose logs [service_name]- Site information setup

Create renewal script:



```bash

nano ssl_renew.sh# Restart specific serviceFor production deployment:

```

docker-compose restart [service_name]

```bash

#!/bin/bash**Explanation:** First-time access shows WordPress installation wizard where you configure site title, admin user, and basic settings.

cd /path/to/wordpress-docker-compose

docker-compose run --rm certbot renew# Rebuild containers

docker-compose exec webserver nginx -s reload

```docker-compose up -d --build --force-recreate1. **Staging (Testing):**



Make executable and add to crontab:```



```bash   - Use `--staging` flag in docker-compose.yml### 3. WordPress Dashboard

chmod +x ssl_renew.sh

crontab -e### Database Connection Issues



# Add this line for daily renewal check at 2 AM   - Test certificate generation**ADD SCREENSHOT HERE: WordPress admin dashboard**

0 2 * * * /path/to/wordpress-docker-compose/ssl_renew.sh

```Verify environment variables are properly set and containers can communicate:



## Troubleshooting**Explanation:** Complete WordPress admin interface showing:



### Check logs:```bash

```bash

docker-compose logs [service_name]# Test database connection from WordPress container2. **Production:**- Dashboard overview

```

docker-compose exec wordpress ping db

### Restart services:

```bash```   - Remove `--staging` flag- Posts and Pages management

docker-compose restart [service_name]

```



### Database backup:### SSL Certificate Issues   - Run `docker-compose down && docker-compose up -d`- Plugin and Theme sections

```bash

docker-compose exec db mysqldump -u root -p${MYSQL_ROOT_PASSWORD} wordpress > backup.sql

```

For Let's Encrypt rate limiting or validation errors:- Settings and customization options

## License



MIT License - Free to use and modify.
1. Use staging environment first (`--staging` flag)## Common Issues

2. Verify DNS records point to your server

3. Ensure ports 80 and 443 are accessible### 4. Live Website

4. Check domain ownership

### MySQL Authentication Error**ADD SCREENSHOT HERE: Frontend "Hello World" post**

## Production Recommendations

**Explanation:** Default WordPress site with "Hello World" post, demonstrating:

- **Backup Strategy:** Regular automated backups of database and WordPress files

- **Updates:** Keep Docker images updated for security patches  **Problem:** MySQL connection authentication method error- Proper theme loading

- **Monitoring:** Implement log monitoring and alerting

- **Firewall:** Configure UFW or iptables to restrict access- Database connectivity

- **Performance:** Consider implementing Redis cache and CDN

**Solution:** Add to MySQL command in docker-compose.yml:- PHP processing working correctly

## License



MIT License - see LICENSE file for details.
```yaml### 5. SSL Certificate Status

command: '--default-authentication-plugin=mysql_native_password'**ADD SCREENSHOT HERE: Browser showing HTTPS lock icon**

```**Explanation:** 

- Green lock icon indicating SSL certificate is active

### Nginx Configuration Mount Error- Certificate details showing Let's Encrypt issuer

- Secure connection established

**Problem:** Directory mount error

### 6. Docker Logs

**Solution:** Ensure nginx-conf is a directory:**ADD SCREENSHOT HERE: `docker-compose logs` output**

```bash

```bashwordpress  | 172.19.0.4 -  23/Sep/2025:20:13:27 +0000 "GET /wp-login.php" 200

mkdir nginx-confwordpress  | 172.19.0.4 -  23/Sep/2025:20:13:29 +0000 "POST /wp-login.php" 302

mv nginx.conf nginx-conf/default.confdb         | 2025-09-23T18:20:26.213201Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections

``````

**Explanation:** Healthy logs showing successful HTTP requests, database connections, and no error messages.

### SSL Certificate Issues

## ⚙️ Configuration Files

**Problem:** Let's Encrypt rate limiting

### docker-compose.yml

**Solution:** Use staging first, then production:```yaml

services:

```bash  db:

# Use --staging flag first    image: mysql:8.0

# Then remove --staging and use --force-renewal    container_name: db

```    restart: unless-stopped

    env_file: .env

## Management Commands    environment:

      - MYSQL_DATABASE=wordpress

### Container Management      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}

      - MYSQL_USER=${MYSQL_USER}

```bash      - MYSQL_PASSWORD=${MYSQL_PASSWORD}

# Stop all services    volumes:

docker-compose down      - dbdata:/var/lib/mysql

    command: '--default-authentication-plugin=mysql_native_password --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci'

# Update and restart    networks:

docker-compose pull      - app-network

docker-compose up -d

  wordpress:

# View logs    depends_on:

docker-compose logs -f      - db

    image: wordpress:5.1.1-fpm-alpine

# Check resource usage    container_name: wordpress

docker stats    restart: unless-stopped

```    env_file: .env

    environment:

### Database Backup      - WORDPRESS_DB_HOST=db:3306

      - WORDPRESS_DB_USER=${MYSQL_USER}

```bash      - WORDPRESS_DB_PASSWORD=${MYSQL_PASSWORD}

# Backup database      - WORDPRESS_DB_NAME=wordpress

docker-compose exec db mysqldump -u root -p${MYSQL_ROOT_PASSWORD} wordpress > backup.sql    volumes:

      - wordpress:/var/www/html

# Restore database    networks:

docker-compose exec -T db mysql -u root -p${MYSQL_ROOT_PASSWORD} wordpress < backup.sql      - app-network

```

  webserver:

## Security Recommendations    depends_on:

      - wordpress

- Use strong, unique passwords    image: nginx:1.15.12-alpine

- Keep Docker images updated    container_name: webserver

- Configure firewall (UFW)    restart: unless-stopped

- Regular security updates    ports:

- Monitor logs regularly      - "80:80"

      - "443:443"

## License    volumes:

      - wordpress:/var/www/html

MIT License - see LICENSE file for details.      - ./nginx-conf:/etc/nginx/conf.d
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
    command: certonly --webroot --webroot-path=/var/www/html --email your-email@domain.com --agree-tos --no-eff-email --force-renewal -d yourdomain.com -d www.yourdomain.com

volumes:
  certbot-etc:
  wordpress:
  dbdata:

networks:
  app-network:
    driver: bridge
```

### Environment Variables (.env)
```bash
MYSQL_ROOT_PASSWORD=strongrootpassword123
MYSQL_USER=wpuser
MYSQL_PASSWORD=wppassword123
```

### Nginx Configuration (nginx-conf/default.conf)
```nginx
server {
    listen 80;
    listen [::]:80;

    server_name yourdomain.com www.yourdomain.com;
    
    index index.php index.html index.htm;
    root /var/www/html;

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

## 🚨 Common Issues & Solutions

### 1. MySQL Authentication Error
**Problem:** `MySQL Connection Error: (2054) The server requested authentication method unknown to the client`

**Solution:** 
```yaml
# In docker-compose.yml, add to MySQL command:
command: '--default-authentication-plugin=mysql_native_password'
```

### 2. Nginx Configuration Mount Error
**Problem:** `not a directory: unknown: Are you trying to mount a directory onto a file`

**Solution:**
```bash
# Ensure nginx-conf is a directory, not a file
mkdir nginx-conf
# Move your config file inside the directory
mv nginx.conf nginx-conf/default.conf
```

### 3. SSL Certificate Issues
**Problem:** Let's Encrypt rate limiting or domain validation fails

**Solution:**
```bash
# Use staging environment first
--staging

# Then switch to production
--force-renewal
```

### 4. WordPress Installation Loop
**Problem:** WordPress keeps showing installation screen

**Solution:**
```bash
# Reset WordPress volume
docker-compose down -v
docker volume rm wordpress-docker-compose_wordpress
docker-compose up -d
```

## 🔒 Security Recommendations

### Production Security Checklist:
- ✅ **Strong database passwords** (generated, not default)
- ✅ **SSL certificates** via Let's Encrypt
- ✅ **Firewall configuration** (UFW on Ubuntu)
- ✅ **Regular updates** of Docker images
- ✅ **WordPress security plugins** (Wordfence, etc.)
- ✅ **Database backups** automated
- ✅ **Non-root user** for server access

### Recommended Updates:
```bash
# Update WordPress image
image: wordpress:latest-fpm-alpine

# Update MySQL image  
image: mysql:8.0-debian

# Update Nginx image
image: nginx:alpine
```

## 📊 Performance Optimization

### Recommended Improvements:
1. **Redis Cache** for object caching
2. **CDN Integration** (CloudFlare)
3. **Image optimization** plugins
4. **Gzip compression** in Nginx
5. **Database optimization** regular maintenance

## 🚀 Deployment Commands

### Complete Deployment Process:
```bash
# 1. Stop existing containers
docker-compose down

# 2. Pull latest images
docker-compose pull

# 3. Start services
docker-compose up -d

# 4. Check status
docker-compose ps

# 5. View logs
docker-compose logs -f

# 6. Backup database
docker-compose exec db mysqldump -u root -p wordpress > backup.sql
```

## 📞 Support & Maintenance

### Regular Maintenance Tasks:
- **Weekly:** Check container status and logs
- **Monthly:** Update Docker images and WordPress plugins
- **Quarterly:** Review security settings and SSL certificates

### Monitoring Commands:
```bash
# Container resource usage
docker stats

# Disk usage
docker system df

# Container health
docker-compose ps
```

## 🏆 Client Success Stories

*"The Docker deployment was flawless! Our WordPress site is now running 3x faster and we can easily scale it. The documentation provided makes it simple for our team to manage. Highly recommended!"* - **Sarah K., E-commerce Business Owner**

*"Perfect implementation of our requirements. SSL certificates work automatically, and the backup system gives us peace of mind. Worth every penny!"* - **Mike R., Digital Agency**

## 🗑️ Cleanup & Destruction Guide

### 🚨 **IMPORTANT: Data Loss Warning**
The following commands will **permanently delete** all WordPress data, databases, and configurations. Make sure to backup anything important before proceeding.

### Complete Cleanup Process:

#### 1. **Stop All Containers**
```bash
cd /path/to/wordpress-docker-compose
docker-compose down
```

#### 2. **Remove Containers and Volumes (DESTRUCTIVE)**
```bash
# Remove containers and all data volumes
docker-compose down -v

# Alternative: Remove everything including images
docker-compose down --rmi all --volumes --remove-orphans
```

#### 3. **Manual Volume Cleanup** (if needed)
```bash
# List all volumes
docker volume ls

# Remove specific project volumes
docker volume rm wordpress-docker-compose_wordpress
docker volume rm wordpress-docker-compose_dbdata
docker volume rm wordpress-docker-compose_certbot-etc

# Force remove if in use
docker volume rm $(docker volume ls -q --filter name=wordpress-docker-compose) --force
```

#### 4. **Remove Docker Images**
```bash
# List images
docker images

# Remove WordPress-related images
docker rmi wordpress:5.1.1-fpm-alpine
docker rmi mysql:8.0
docker rmi nginx:1.15.12-alpine
docker rmi certbot/certbot

# Remove all unused images
docker image prune -a --force
```

#### 5. **Remove Docker Networks**
```bash
# List networks
docker network ls

# Remove project network
docker network rm wordpress-docker-compose_app-network

# Clean unused networks
docker network prune --force
```

#### 6. **Complete Docker System Cleanup**
```bash
# Remove everything unused (NUCLEAR OPTION)
docker system prune -a --volumes --force

# Check remaining resources
docker system df
```

#### 7. **Remove Project Files** (Optional)
```bash
# Remove entire project directory
rm -rf /path/to/wordpress-docker-compose

# On Windows:
# rmdir /s wordpress-docker-compose
```

### 🔍 **Verification Commands**

After cleanup, verify everything is removed:
```bash
# Check containers
docker ps -a

# Check volumes  
docker volume ls

# Check images
docker images

# Check networks
docker network ls

# Check system usage
docker system df
```

### 📦 **Backup Before Destruction**

Always backup important data before cleanup:

#### **Database Backup:**
```bash
# Export WordPress database
docker-compose exec db mysqldump -u root -p${MYSQL_ROOT_PASSWORD} wordpress > wordpress_backup.sql

# Or backup with docker command
docker exec db mysqldump -u root -p${MYSQL_ROOT_PASSWORD} wordpress > wordpress_backup.sql
```

#### **WordPress Files Backup:**
```bash
# Copy WordPress files from container
docker cp wordpress:/var/www/html ./wordpress_files_backup

# Or create archive
docker run --rm -v wordpress-docker-compose_wordpress:/data -v $(pwd):/backup alpine tar czf /backup/wordpress_backup.tar.gz -C /data .
```

#### **Configuration Backup:**
```bash
# Backup your project files
cp docker-compose.yml docker-compose.yml.backup
cp .env .env.backup
cp -r nginx-conf nginx-conf.backup
```

### 🚀 **Quick Cleanup Commands**

For different scenarios:

#### **Development Reset** (Keep images, remove data)
```bash
docker-compose down -v
docker-compose up -d
```

#### **Complete Project Removal**
```bash
docker-compose down --rmi all --volumes --remove-orphans
cd ..
rm -rf wordpress-docker-compose
```

#### **Docker System Reset** (Nuclear option)
```bash
docker stop $(docker ps -aq)
docker rm $(docker ps -aq)  
docker rmi $(docker images -q)
docker volume rm $(docker volume ls -q)
docker network rm $(docker network ls -q)
docker system prune -a --volumes --force
```

### ⚠️ **What Gets Destroyed:**

| Component | What's Lost | Backup Method |
|-----------|-------------|---------------|
| **WordPress Database** | All posts, pages, users, settings | `mysqldump` |
| **WordPress Files** | Themes, plugins, uploads | `docker cp` |
| **SSL Certificates** | Let's Encrypt certificates | Re-generate automatically |
| **Container Logs** | Application logs | `docker-compose logs > logs.txt` |
| **Custom Configurations** | nginx.conf, docker-compose.yml | Git repository |

### 🔄 **Disaster Recovery**

If you need to restore after cleanup:

#### **1. Restore Database:**
```bash
# Start containers
docker-compose up -d db

# Import backup
docker-compose exec -T db mysql -u root -p${MYSQL_ROOT_PASSWORD} wordpress < wordpress_backup.sql
```

#### **2. Restore WordPress Files:**
```bash
# Extract backup
docker run --rm -v wordpress-docker-compose_wordpress:/data -v $(pwd):/backup alpine tar xzf /backup/wordpress_backup.tar.gz -C /data
```

#### **3. Restart All Services:**
```bash
docker-compose down
docker-compose up -d
```

### 🛠️ **Maintenance vs Destruction**

| Task | Command | Data Loss | Use Case |
|------|---------|-----------|----------|
| **Restart Services** | `docker-compose restart` | ❌ None | Configuration changes |
| **Update Images** | `docker-compose pull && docker-compose up -d` | ❌ None | Security updates |
| **Reset WordPress** | `docker-compose down -v` | ⚠️ WordPress data | Fresh installation |
| **Complete Cleanup** | `docker-compose down --rmi all --volumes` | 🚨 Everything | Project removal |

### 📞 **When to Use Cleanup:**

✅ **Safe to cleanup when:**
- Moving to a different server
- Project completed/cancelled
- Testing/development environment
- Switching to different approach

❌ **Don't cleanup when:**
- Production environment with live data
- No backup exists
- Unsure about data importance
- Client still needs access

---

**Remember: Docker containers are disposable, but your data is precious! Always backup first!** 💾

## 📧 Contact

For WordPress Docker deployment services and DevOps consulting:
- **Upwork Profile:** [Your Profile Link]
- **Email:** your.email@domain.com
- **Portfolio:** [Your Portfolio Link]

---

**Ready to containerize your WordPress site? Let's make it happen!** 🚀

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

*Built with ❤️ for the WordPress community*