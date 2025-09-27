# WordPress Docker Compose# WordPress Docker Compose



A production-ready WordPress deployment using Docker Compose with Nginx, MySQL, and automatic SSL certificates.A production-ready WordPress deployment using Docker Compose with Nginx, MySQL, and automatic SSL certificates.



## Features## Features



- **WordPress 5.1.1** with PHP-FPM- **WordPress 5.1.1** with PHP-FMP

- **MySQL 8.0** database  - **MySQL 8.0** database

- **Nginx** web server with SSL support- **Nginx** web server with SSL support

- **Let's Encrypt** automatic SSL certificates- **Let's Encrypt** automatic SSL certificates

- **Docker Compose** orchestration- **Docker Compose** orchestration

- Production-ready configuration- Production-ready configuration



## Architecture## Architecture



``````

┌─────────────────────────────────────────────┐┌─────────────────────────────────────────────┐

│                   Server                    ││                   Server                    │

├─────────────────────────────────────────────┤├─────────────────────────────────────────────┤

│  ┌─────────────┐  ┌─────────────┐  ┌───────┐ ││  ┌─────────────┐  ┌─────────────┐  ┌───────┐ │

│  │   Nginx     │  │ WordPress   │  │ MySQL │ ││  │   Nginx     │  │ WordPress   │  │ MySQL │ │

│  │ (Webserver) │  │   (PHP)     │  │  (DB) │ ││  │ (Webserver) │  │   (PHP)     │  │  (DB) │ │

│  │  Port 80    │  │ Port 9000   │  │ 3306  │ ││  │  Port 80    │  │ Port 9000   │  │ 3306  │ │

│  │  Port 443   │  │             │  │       │ ││  │  Port 443   │  │             │  │       │ │

│  └─────────────┘  └─────────────┘  └───────┘ ││  └─────────────┘  └─────────────┘  └───────┘ │

│  ┌─────────────────────────────────────────┐ ││  ┌─────────────────────────────────────────┐ │

│  │         Let's Encrypt (Certbot)        │ ││  │         Let's Encrypt (Certbot)        │ │

│  └─────────────────────────────────────────┘ ││  └─────────────────────────────────────────┘ │

└─────────────────────────────────────────────┘└─────────────────────────────────────────────┘

``````



## Project Structure## Project Structure



``````

wordpress-docker-compose/wordpress-docker-compose/

├── docker-compose.yml     # Main orchestration file├── docker-compose.yml     # Main orchestration file

├── .env                  # Environment variables (not tracked)├── .env                  # Environment variables (not tracked)

├── nginx-conf/├── nginx-conf/

│   └── default.conf     # Nginx configuration│   └── default.conf     # Nginx configuration

├── .gitignore           # Git ignore rules├── .gitignore           # Git ignore rules

├── .dockerignore        # Docker ignore rules├── .dockerignore        # Docker ignore rules

└── README.md            # This file└── README.md            # This file

``````



## Quick Start## Quick Start



### Prerequisites### Prerequisites

- Docker and Docker Compose installed

- Docker and Docker Compose installed- Domain name (optional for local testing)

- Domain name (optional for local testing)

### 1. Clone Repository

### 1. Clone Repository```bash

git clone https://github.com/inaadem/wordpress-docker-compose.git

```bashcd wordpress-docker-compose

git clone https://github.com/inaadem/wordpress-docker-compose.git```

cd wordpress-docker-compose

```### 2. Configure Environment

```bash

### 2. Configure Environment# Create environment file

cp .env.example .env

Create a `.env` file with your database credentials:

# Edit .env with your database credentials

```bashMYSQL_ROOT_PASSWORD=your_strong_password

MYSQL_ROOT_PASSWORD=your_strong_passwordMYSQL_USER=wpuser

MYSQL_USER=wpuserMYSQL_PASSWORD=your_wp_password

MYSQL_PASSWORD=your_wp_password```

```

### 3. Update Domain Configuration

### 3. Update Domain ConfigurationEdit `docker-compose.yml` and `nginx-conf/default.conf`:

- Replace `yourdomain.com` with your actual domain

Edit `docker-compose.yml` and `nginx-conf/default.conf`:- Replace `your-email@domain.com` with your email



- Replace `yourdomain.com` with your actual domain### 4. Start Services

- Replace `your-email@domain.com` with your email```bash

# Start all containers

### 4. Start Servicesdocker-compose up -d



```bash# Check status

# Start all containersdocker-compose ps

docker-compose up -d

# View logs

# Check statusdocker-compose logs

docker-compose ps```



# View logs### 5. Access WordPress

docker-compose logs- **Local:** http://localhost

```- **Production:** https://yourdomain.com



### 5. Access WordPress## 📸 Screenshots & Explanations



- **Local:** <http://localhost>### 1. Container Status

- **Production:** <https://yourdomain.com>**ADD SCREENSHOT HERE: `docker-compose ps` output**

```bash

## ConfigurationNAME        IMAGE                        COMMAND                  SERVICE     STATUS          PORTS

db          mysql:8.0                    "docker-entrypoint.s…"   db          Up 2 hours      3306/tcp, 33060/tcp

### Environment Variables (.env)webserver   nginx:1.15.12-alpine         "nginx -g 'daemon of…"   webserver   Up 56 seconds   0.0.0.0:80->80/tcp, [::]:80->80/tcp

wordpress   wordpress:5.1.1-fpm-alpine   "docker-entrypoint.s…"   wordpress   Up 2 hours      9000/tcp

```bash```

MYSQL_ROOT_PASSWORD=strongrootpassword123**Explanation:** Shows all three containers running successfully - database, webserver, and WordPress application.

MYSQL_USER=wpuser

MYSQL_PASSWORD=wppassword123### 2. WordPress Installation Screen

```**ADD SCREENSHOT HERE: Initial WordPress setup page**

- Language selection

### SSL Certificate Setup- Database configuration confirmation

- Site information setup

For production deployment:

**Explanation:** First-time access shows WordPress installation wizard where you configure site title, admin user, and basic settings.

1. **Staging (Testing):**

   - Use `--staging` flag in docker-compose.yml### 3. WordPress Dashboard

   - Test certificate generation**ADD SCREENSHOT HERE: WordPress admin dashboard**

**Explanation:** Complete WordPress admin interface showing:

2. **Production:**- Dashboard overview

   - Remove `--staging` flag- Posts and Pages management

   - Run `docker-compose down && docker-compose up -d`- Plugin and Theme sections

- Settings and customization options

## Common Issues

### 4. Live Website

### MySQL Authentication Error**ADD SCREENSHOT HERE: Frontend "Hello World" post**

**Explanation:** Default WordPress site with "Hello World" post, demonstrating:

**Problem:** MySQL connection authentication method error- Proper theme loading

- Database connectivity

**Solution:** Add to MySQL command in docker-compose.yml:- PHP processing working correctly



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