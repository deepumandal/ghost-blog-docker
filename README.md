# 🚀 Ghost Blog Docker Setup

A complete, production-ready Docker configuration for self-hosting Ghost CMS with MySQL database, Nginx reverse proxy, and HTTPS support.

[![Ghost Version](https://img.shields.io/badge/Ghost-v5%2B-brightgreen)](https://ghost.org/)
[![Docker](https://img.shields.io/badge/Docker-Ready-blue)](https://www.docker.com/)
[![MySQL](https://img.shields.io/badge/MySQL-5.7-orange)](https://www.mysql.com/)

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [Directory Structure](#directory-structure)
- [Container Management](#container-management)
- [Production Deployment](#production-deployment)
- [Backup & Restore](#backup--restore)
- [Troubleshooting](#troubleshooting)
- [Security Considerations](#security-considerations)
- [Contributing](#contributing)

---

## 🎯 Overview

This project provides a containerized Ghost CMS setup using Docker Compose, designed for both development and production environments. Ghost is a modern, open-source publishing platform built on Node.js, perfect for blogs, magazines, and content-driven websites.

### Key Benefits
- **Isolated Environment**: Complete application stack in containers
- **Easy Deployment**: One-command setup and deployment
- **Data Persistence**: MySQL database with persistent volumes
- **Scalable**: Ready for production with nginx reverse proxy
- **Secure**: Environment-based configuration management

---

## ✨ Features

- 🔥 **Ghost CMS v5+** - Latest Alpine-based Ghost image
- 🐬 **MySQL 5.7** - Reliable relational database with persistent storage
- 🔒 **Environment Security** - Secure credential management via `.env` files
- 🌐 **Nginx Ready** - Production-ready reverse proxy configuration
- 📦 **Docker Compose** - Multi-container orchestration
- 🔄 **Auto-restart** - Containers restart automatically on failure
- 📁 **Volume Persistence** - Data survives container restarts
- 🛡️ **Network Isolation** - Internal Docker network for secure communication

---

## 🏗️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│      Nginx      │    │      Ghost      │    │      MySQL      │
│   (Port 80/443) │◄──►│   (Port 2368)   │◄──►│   (Port 3306)   │
│  Load Balancer  │    │   CMS Server    │    │    Database     │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         │                       │                       │
    ┌────▼────┐             ┌────▼────┐             ┌────▼────┐
    │ nginx/  │             │ghost-   │             │ghost-db/│
    │ config  │             │content/ │             │ data    │
    └─────────┘             └─────────┘             └─────────┘
```

### Network Flow
1. **External Traffic** → Nginx (reverse proxy)
2. **Nginx** → Ghost container (internal network)
3. **Ghost** → MySQL container (internal network)
4. **Data Persistence** → Host volumes

---

## 📋 Prerequisites

Before you begin, ensure you have the following installed:

- **Docker** (v20.10+)
  ```bash
  docker --version
  ```
- **Docker Compose** (v2.0+)
  ```bash
  docker compose version
  ```
- **Git** (for cloning)
  ```bash
  git --version
  ```

### System Requirements
- **RAM**: Minimum 1GB, Recommended 2GB+
- **Storage**: At least 5GB free space
- **OS**: Linux, macOS, or Windows with WSL2

---

## 🚀 Quick Start

### 1️⃣ Clone the Repository
```bash
git clone https://github.com/deepumandal/ghost-blog-docker.git
cd ghost-blog-docker
```

### 2️⃣ Configure Environment
```bash
# Copy the example environment file
cp .env.example .env

# Edit the environment variables
nano .env  # or use your preferred editor
```

**Required Environment Variables:**
```bash
MYSQL_ROOT_PASSWORD=your_secure_root_password
MYSQL_DATABASE=ghost
MYSQL_USER=ghost
MYSQL_PASSWORD=your_secure_ghost_password
GHOST_URL=http://localhost:2368  # Change for production
```

### 3️⃣ Start the Services
```bash
# Start all services in background
docker compose up -d

# Check service status
docker compose ps
```

### 4️⃣ Access Your Ghost Site
- **Frontend**: http://localhost:2368
- **Admin Panel**: http://localhost:2368/ghost

### 5️⃣ Initial Setup
1. Navigate to the admin panel
2. Create your admin account
3. Configure your site settings
4. Start publishing content!

---

## ⚙️ Configuration

### Environment Variables

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| `MYSQL_ROOT_PASSWORD` | MySQL root password | - | ✅ |
| `MYSQL_DATABASE` | Ghost database name | `ghost` | ✅ |
| `MYSQL_USER` | Ghost database user | `ghost` | ✅ |
| `MYSQL_PASSWORD` | Ghost database password | - | ✅ |
| `GHOST_URL` | Public URL of your Ghost site | `http://localhost:2368` | ✅ |

### Ghost Configuration

Ghost uses environment variables for configuration. Key settings include:

- **Database Connection**: Automatically configured via environment variables
- **URL**: Set via `GHOST_URL` for proper link generation
- **Content Path**: Mounted to `./ghost-content` for persistence

### MySQL Configuration

- **Version**: MySQL 5.7 (stable and Ghost-compatible)
- **Character Set**: UTF8MB4 for full Unicode support
- **Data Directory**: Persisted to `./ghost-db`

---

## 📁 Directory Structure

```
ghost-blog-docker/
├── 📄 docker-compose.yml          # Main orchestration file
├── 📄 .env.example               # Environment template
├── 📄 .env                       # Your environment config (not tracked)
├── 📄 .gitignore                 # Git ignore rules
├── 📄 README.md                  # This documentation
├── 📁 ghost-content/             # Ghost CMS data (persistent)
│   ├── 📁 apps/                  # Custom Ghost apps
│   ├── 📁 data/                  # Ghost database exports
│   ├── 📁 files/                 # File uploads
│   ├── 📁 images/                # Image uploads
│   ├── 📁 logs/                  # Ghost application logs
│   ├── 📁 media/                 # Media files
│   ├── 📁 public/                # Public assets
│   ├── 📁 settings/              # Ghost settings
│   └── 📁 themes/                # Custom themes
├── 📁 ghost-db/                  # MySQL data (persistent)
│   └── 📁 ghost/                 # Ghost database tables
└── 📁 nginx/                     # Nginx configuration
    └── 📄 nginx.conf             # Reverse proxy config
```

### Volume Mounts

| Host Path | Container Path | Purpose |
|-----------|----------------|---------|
| `./ghost-content` | `/var/lib/ghost/content` | Ghost CMS content & uploads |
| `./ghost-db` | `/var/lib/mysql` | MySQL database files |

---

## 🐳 Container Management

### Basic Commands

```bash
# Start all services
docker compose up -d

# Stop all services
docker compose down

# View running containers
docker compose ps

# View logs
docker compose logs ghost
docker compose logs ghost-db

# Follow logs in real-time
docker compose logs -f ghost

# Restart a specific service
docker compose restart ghost

# Update to latest images
docker compose pull
docker compose up -d
```

### Service Health Checks

```bash
# Check container health
docker compose ps

# Detailed container information
docker inspect ghost
docker inspect ghost-db

# Enter container for debugging
docker compose exec ghost sh
docker compose exec ghost-db mysql -u root -p
```

### Resource Monitoring

```bash
# View container resource usage
docker stats

# View disk usage
docker system df

# Clean up unused resources
docker system prune
```

---

## 🌐 Production Deployment

### 1️⃣ Domain Configuration

Update your `.env` file for production:
```bash
GHOST_URL=https://yourdomain.com
```

### 2️⃣ Nginx Configuration

Configure `nginx/nginx.conf` for SSL termination:

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    
    # Redirect HTTP to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name yourdomain.com www.yourdomain.com;
    
    # SSL Configuration
    ssl_certificate /path/to/your/certificate.pem;
    ssl_certificate_key /path/to/your/private.key;
    
    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    
    location / {
        proxy_pass http://ghost:2368;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### 3️⃣ Let's Encrypt SSL

For automatic SSL certificates:

```bash
# Install certbot
sudo apt install certbot python3-certbot-nginx

# Obtain certificate
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Auto-renewal (add to crontab)
0 12 * * * /usr/bin/certbot renew --quiet
```

### 4️⃣ Security Hardening

1. **Use strong passwords** in `.env`
2. **Enable firewall** rules
3. **Regular updates** of images
4. **Monitor logs** for suspicious activity
5. **Backup regularly**

---

## 💾 Backup & Restore

### Database Backup

```bash
# Create MySQL backup
docker compose exec ghost-db mysqldump -u root -p$MYSQL_ROOT_PASSWORD ghost > backup_$(date +%Y%m%d_%H%M%S).sql

# Automated backup script
#!/bin/bash
BACKUP_DIR="/backups"
DATE=$(date +%Y%m%d_%H%M%S)
docker compose exec ghost-db mysqldump -u root -p$MYSQL_ROOT_PASSWORD ghost > $BACKUP_DIR/ghost_db_$DATE.sql
```

### Content Backup

```bash
# Backup Ghost content directory
tar -czf ghost_content_$(date +%Y%m%d_%H%M%S).tar.gz ghost-content/

# Backup everything
tar -czf ghost_full_backup_$(date +%Y%m%d_%H%M%S).tar.gz \
    --exclude='ghost-db' \
    --exclude='.git' \
    .
```

### Restore Process

```bash
# Stop services
docker compose down

# Restore database
docker compose up -d ghost-db
docker compose exec -T ghost-db mysql -u root -p$MYSQL_ROOT_PASSWORD ghost < backup.sql

# Restore content
tar -xzf ghost_content_backup.tar.gz

# Start all services
docker compose up -d
```

---

## 🔧 Troubleshooting

### Common Issues

#### 1. Database Connection Failed
```bash
# Check MySQL container logs
docker compose logs ghost-db

# Verify environment variables
docker compose exec ghost env | grep database

# Test database connection
docker compose exec ghost-db mysql -u $MYSQL_USER -p$MYSQL_PASSWORD $MYSQL_DATABASE
```

#### 2. Port Already in Use
```bash
# Check what's using port 2368
netstat -tulpn | grep 2368

# Use different port in docker-compose.yml
ports:
  - "3000:2368"  # Change host port
```

#### 3. Permission Issues
```bash
# Fix ownership of mounted volumes
sudo chown -R 1000:1000 ghost-content/
sudo chown -R 999:999 ghost-db/
```

#### 4. Ghost Won't Start
```bash
# Check Ghost logs
docker compose logs ghost

# Verify Ghost URL configuration
docker compose exec ghost cat config.production.json
```

### Log Locations

- **Ghost Logs**: `docker compose logs ghost`
- **MySQL Logs**: `docker compose logs ghost-db`
- **Application Logs**: `ghost-content/logs/`

### Performance Optimization

```bash
# Monitor resource usage
docker stats

# Optimize MySQL
# Add to docker-compose.yml under ghost-db service:
command: --innodb-buffer-pool-size=256M --max-connections=50
```

---

## 🛡️ Security Considerations

### 1. Environment Security
- Never commit `.env` files
- Use strong, unique passwords
- Rotate passwords regularly

### 2. Network Security
- Use internal Docker networks
- Expose only necessary ports
- Implement firewall rules

### 3. Container Security
- Regular image updates
- Use official images only
- Scan for vulnerabilities

### 4. Data Security
- Regular backups
- Encrypt sensitive data
- Monitor access logs

### 5. Access Control
- Strong admin passwords
- Two-factor authentication
- Limited user permissions

---

## 🤝 Contributing

### Development Setup

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

### Code Style

- Use clear, descriptive commit messages
- Follow Docker best practices
- Update documentation for changes
- Test in both development and production modes

### Reporting Issues

Please include:
- Operating system and version
- Docker and Docker Compose versions
- Steps to reproduce
- Error logs and screenshots

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- [Ghost CMS](https://ghost.org/) - The publishing platform
- [Docker](https://www.docker.com/) - Containerization platform
- [MySQL](https://www.mysql.com/) - Database system
- [Nginx](https://nginx.org/) - Web server and reverse proxy

---

## 📞 Support

For support and questions:

- 📧 Email: [dev.workwithdeepak@gmail.com](mailto:dev.workwithdeepak@gmail.com)
- 🐛 Issues: [GitHub Issues](https://github.com/deepumandal/ghost-blog-docker/issues)
- 💬 Community: [Ghost Forum](https://forum.ghost.org/)

---

<div align="center">

**⭐ Star this repository if it helped you!**

Made with ❤️ by [Deepu Mandal](https://github.com/deepumandal)

</div>
