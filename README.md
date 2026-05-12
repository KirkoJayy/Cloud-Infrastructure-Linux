# CloudArmor – Secure Cloud Infrastructure Capstone

![Project Type](https://img.shields.io/badge/Project-Cloud%20Infrastructure-blue)
![Platform](https://img.shields.io/badge/Platform-DigitalOcean-blue)
![OS](https://img.shields.io/badge/OS-Ubuntu%20Server-orange)
![Containers](https://img.shields.io/badge/Containers-Docker-blue)
![Security](https://img.shields.io/badge/Security-SSL%2FTLS%20%7C%20UFW%20%7C%20SSH%20Hardening-red)
![Database](https://img.shields.io/badge/Database-MariaDB-green)
![Application](https://img.shields.io/badge/Application-Nextcloud-lightgrey)

## Overview

**CloudArmor** is a secure cloud infrastructure capstone project that demonstrates how to deploy, secure, and operate a self-hosted private cloud collaboration platform.

The project uses a cloud-hosted Linux server to run a containerized **Nextcloud** environment supported by **MariaDB**, **Nginx reverse proxying**, **DNS configuration**, **SSL/TLS encryption**, **UFW firewall controls**, **SSH hardening**, and **backup scripting**.

The purpose of this project was not only to deploy an application, but to build a realistic infrastructure environment that includes security, persistence, remote administration, recovery planning, and operational documentation.

---

## Project Summary

CloudArmor provides a private cloud collaboration platform where users can securely access a web-based file-sharing service through a domain name over HTTPS.

At a high level, the project includes:

- Cloud-hosted Linux virtual machine
- Public domain routing
- Docker-based application deployment
- Nextcloud application container
- MariaDB database backend
- Nginx reverse proxy
- SSL/TLS encryption
- UFW firewall rules
- SSH hardening
- Persistent storage
- Database backup workflow
- Deployment and troubleshooting documentation

---

## Architecture

```text
User Browser
    |
    | HTTPS
    v
Domain / DNS
    |
    v
Cloud VM
    |
    v
UFW Firewall
    |
    v
Nginx Reverse Proxy
    |
    v
Nextcloud Application Container
    |
    +----------------------+
    |                      |
    v                      v
MariaDB Database      Persistent Storage
    |
    v
Backup Workflow
```

---

## Technology Stack

| Category | Technology |
|---|---|
| Cloud Provider | DigitalOcean or equivalent cloud VM provider |
| Operating System | Ubuntu Server |
| Application | Nextcloud |
| Database | MariaDB |
| Web Proxy | Nginx |
| Containerization | Docker / Docker Compose |
| Security | SSL/TLS, UFW, SSH hardening |
| DNS | Custom domain A record |
| Backup | Bash scripting and database dumps |
| Administration | Linux CLI, SSH |

---

## Core Features

- Public-facing private cloud application
- Domain-based access instead of raw IP access
- HTTPS-secured user traffic
- Reverse proxy routing through Nginx
- Containerized application and database services
- Persistent storage for application data
- MariaDB-backed application state
- Firewall-restricted inbound access
- Hardened SSH administration
- Backup scripting for database recovery
- Reproducible deployment structure

---

## Infrastructure Components

### Cloud VM

The project is hosted on a Linux-based cloud virtual machine. The VM provides the compute, storage, public network interface, and operating system layer required to run the CloudArmor stack.

The VM is responsible for:

- Hosting Docker containers
- Receiving web traffic
- Running the reverse proxy
- Managing firewall rules
- Supporting remote SSH administration
- Storing application and database data
- Running backup scripts

---

### DNS

DNS maps the CloudArmor domain to the public IP address of the cloud VM.

Example DNS flow:

```text
cloudarmor.example.com -> Cloud VM Public IP
```

This allows users to access the application through a readable domain name rather than directly using an IP address.

For security and portfolio use, this repository uses placeholder values instead of exposing the real domain or public IP.

---

### Nginx Reverse Proxy

Nginx is used as a reverse proxy in front of the Nextcloud application.

The reverse proxy provides:

- Public web entry point
- HTTP/HTTPS routing
- Cleaner domain-based access
- Separation between external traffic and internal application containers
- A scalable pattern for adding future services

Traffic flow:

```text
User Request
    |
    v
Nginx Reverse Proxy
    |
    v
Nextcloud Container
```

---

### Nextcloud

Nextcloud is the main application layer of the project. It provides the web interface for file storage, uploads, downloads, account access, and private cloud collaboration.

Nextcloud was selected because it behaves like a real infrastructure workload. It requires:

- Database connectivity
- Persistent storage
- HTTPS access
- Reverse proxy configuration
- User authentication
- Backup and recovery planning

---

### MariaDB

MariaDB is the database backend for Nextcloud.

It stores:

- User metadata
- Application settings
- File index records
- Sharing data
- Internal Nextcloud configuration
- Application state

MariaDB should remain private and should not be exposed directly to the public internet.

---

### SSL / TLS

SSL/TLS is used to encrypt traffic between the user and the CloudArmor platform.

This protects:

- Login credentials
- Session data
- Uploaded files
- Downloaded files
- Web application traffic

HTTPS is required for a secure production-style deployment.

---

### UFW Firewall

UFW is used to restrict inbound traffic to only the required services.

Recommended firewall model:

| Port | Service | Exposure |
|---|---|---|
| 80/tcp | HTTP / certificate validation | Public |
| 443/tcp | HTTPS | Public |
| Custom SSH Port | Remote administration | Restricted |
| 3306/tcp | MariaDB | Blocked from public internet |
| Internal Docker Ports | Backend services | Not publicly exposed |

Example commands:

```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow <SSH_PORT>/tcp
sudo ufw enable
sudo ufw status verbose
```

---

### SSH Hardening

SSH hardening reduces the attack surface of the cloud server.

Recommended SSH hardening controls:

- Use SSH key-based authentication
- Disable direct root login
- Disable password authentication where possible
- Use a non-default SSH port
- Restrict SSH access with firewall rules
- Use a non-root administrative user with sudo access

Example `/etc/ssh/sshd_config` settings:

```text
Port <SSH_PORT>
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

Restart SSH after changes:

```bash
sudo systemctl restart ssh
```

> Always test a second SSH session before closing the original session to avoid locking yourself out.

---

## Recommended Repository Structure

```text
CloudArmor/
│
├── README.md
├── docker-compose.yml
├── .env.example
├── .gitignore
│
├── nginx/
│   └── default.conf
│
├── scripts/
│   ├── backup-db.sh
│   ├── restore-db.sh
│   └── health-check.sh
│
├── docs/
│   ├── architecture.md
│   ├── deployment-guide.md
│   ├── security-model.md
│   ├── backup-restore-runbook.md
│   └── troubleshooting.md
│
├── diagrams/
│   └── architecture.png
│
└── screenshots/
    ├── nextcloud-login.png
    ├── nextcloud-dashboard.png
    ├── ssl-enabled.png
    ├── ufw-status.png
    ├── docker-containers.png
    └── backup-output.png
```

---

## Environment Configuration

Sensitive values should be stored in a local `.env` file.

Do **not** commit the real `.env` file to GitHub.

Instead, commit a sanitized `.env.example` file that shows the required variables without exposing secrets.

Example:

```env
DOMAIN_NAME=cloudarmor.example.com
SERVER_PUBLIC_IP=203.0.113.10
SSH_PORT=2222

MYSQL_ROOT_PASSWORD=replace_with_secure_root_password
MYSQL_DATABASE=nextcloud
MYSQL_USER=nextcloud_user
MYSQL_PASSWORD=replace_with_secure_database_password

NEXTCLOUD_ADMIN_USER=admin
NEXTCLOUD_ADMIN_PASSWORD=replace_with_secure_admin_password
NEXTCLOUD_TRUSTED_DOMAINS=cloudarmor.example.com

LETSENCRYPT_EMAIL=admin@example.com
```

---

## Example Docker Compose Structure

> This is a sanitized example. Version numbers, domains, passwords, and deployment details should be adjusted for the actual environment.

```yaml
services:
  db:
    image: mariadb:11
    container_name: cloudarmor-db
    restart: unless-stopped
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - cloudarmor_internal

  app:
    image: nextcloud:29
    container_name: cloudarmor-nextcloud
    restart: unless-stopped
    depends_on:
      - db
    environment:
      MYSQL_HOST: db
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
      NEXTCLOUD_ADMIN_USER: ${NEXTCLOUD_ADMIN_USER}
      NEXTCLOUD_ADMIN_PASSWORD: ${NEXTCLOUD_ADMIN_PASSWORD}
      NEXTCLOUD_TRUSTED_DOMAINS: ${NEXTCLOUD_TRUSTED_DOMAINS}
    volumes:
      - nextcloud_data:/var/www/html
    networks:
      - cloudarmor_internal
      - cloudarmor_proxy

  nginx:
    image: nginx:1.27
    container_name: cloudarmor-nginx
    restart: unless-stopped
    depends_on:
      - app
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
    networks:
      - cloudarmor_proxy

volumes:
  db_data:
  nextcloud_data:

networks:
  cloudarmor_internal:
    internal: true
  cloudarmor_proxy:
```

---

## Deployment Guide

### 1. Provision the Cloud Server

Create a cloud VM using Ubuntu Server.

Recommended baseline:

| Resource | Recommendation |
|---|---|
| OS | Ubuntu Server 22.04 LTS or newer |
| CPU | 1–2 vCPU minimum |
| RAM | 2–4 GB minimum |
| Storage | 25–50 GB minimum |
| Public IP | Required |
| Domain | Recommended |

---

### 2. Connect to the Server

Use SSH with sanitized placeholders:

```bash
ssh -i ~/.ssh/cloudarmor_key -p <SSH_PORT> <ADMIN_USER>@<SERVER_PUBLIC_IP>
```

---

### 3. Update the Server

```bash
sudo apt update
sudo apt upgrade -y
```

---

### 4. Install Docker and Docker Compose

```bash
sudo apt install -y docker.io docker-compose-plugin
sudo systemctl enable docker
sudo systemctl start docker
```

Verify installation:

```bash
docker --version
docker compose version
```

---

### 5. Clone the Repository

```bash
git clone https://github.com/<YOUR_USERNAME>/CloudArmor.git
cd CloudArmor
```

---

### 6. Create the Local Environment File

```bash
cp .env.example .env
nano .env
```

Update the values with environment-specific secrets and domain information.

---

### 7. Configure DNS

Create an A record pointing the domain to the cloud VM public IP.

Example:

```text
Type: A
Name: @
Value: <SERVER_PUBLIC_IP>
TTL: Automatic
```

Optional:

```text
Type: A
Name: www
Value: <SERVER_PUBLIC_IP>
TTL: Automatic
```

Verify DNS:

```bash
dig <DOMAIN_NAME>
nslookup <DOMAIN_NAME>
```

---

### 8. Start the Application Stack

```bash
docker compose up -d
```

Verify containers:

```bash
docker ps
```

---

### 9. Validate Web Access

Open the application in a browser:

```text
https://<DOMAIN_NAME>
```

Also verify from the terminal:

```bash
curl -I https://<DOMAIN_NAME>
```

---

### 10. Validate Nextcloud

Recommended validation checklist:

- Nextcloud login page loads
- Admin account can log in
- Test user can be created
- File upload succeeds
- File download succeeds
- Data persists after container restart
- MariaDB container remains running
- Nginx routes traffic correctly
- HTTPS certificate is valid

---

## Backup and Recovery

### Database Backup

Example database backup script:

```bash
#!/bin/bash

set -e

DATE=$(date +%F-%H-%M)
BACKUP_DIR="./backups/db"

mkdir -p "$BACKUP_DIR"

docker exec cloudarmor-db mariadb-dump \
  -u root \
  -p"$MYSQL_ROOT_PASSWORD" \
  --all-databases > "$BACKUP_DIR/cloudarmor-db-$DATE.sql"

echo "Database backup completed: $BACKUP_DIR/cloudarmor-db-$DATE.sql"
```

### File Backup

Example file backup command:

```bash
docker run --rm \
  -v cloudarmor_nextcloud_data:/data \
  -v "$(pwd)/backups/files:/backup" \
  ubuntu tar czf /backup/nextcloud-files-$(date +%F-%H-%M).tar.gz /data
```

### Recovery Planning

A complete recovery plan should include:

1. Rebuild the cloud VM if needed
2. Reinstall Docker and Docker Compose
3. Restore the project repository
4. Restore the `.env` file from a secure location
5. Restore MariaDB database dump
6. Restore Nextcloud files
7. Restart containers
8. Validate login and file access

Recommended recovery targets:

| Metric | Target |
|---|---|
| RPO | 24 hours or less |
| RTO | 1–4 hours |

---

## Security Model

CloudArmor applies layered security controls across the cloud, operating system, application, and network layers.

| Layer | Control |
|---|---|
| Cloud | VM firewall/security group rules |
| Network | UFW inbound restrictions |
| Web | HTTPS through SSL/TLS |
| Proxy | Nginx reverse proxy |
| Administration | SSH hardening |
| Application | Nextcloud user authentication |
| Database | Private MariaDB container network |
| Secrets | `.env` file excluded from GitHub |
| Recovery | Backup scripting |

---

## Security Best Practices

Before publishing this project publicly:

- Remove all real credentials
- Remove real public IP addresses
- Remove private SSH keys
- Remove real admin passwords
- Remove database passwords
- Remove screenshots that expose sensitive values
- Rotate credentials if they were ever committed or shared
- Use `.env.example` instead of `.env`
- Add a `.gitignore`
- Review commit history for exposed secrets

Recommended `.gitignore`:

```gitignore
.env
*.key
*.pem
id_rsa
id_ed25519
secrets/
backups/
*.sql
*.tar
*.tar.gz
*.zip
```

---

## Testing and Validation

| Test | Method | Expected Result |
|---|---|---|
| DNS Resolution | `dig <DOMAIN_NAME>` | Domain resolves to server IP |
| HTTPS Access | Browser / `curl -I` | Valid HTTPS response |
| Container Status | `docker ps` | Required containers are running |
| Reverse Proxy | Visit domain | Nginx routes to Nextcloud |
| Login Test | Browser | Admin login succeeds |
| File Upload | Nextcloud UI | Upload succeeds |
| File Persistence | Restart containers | File remains available |
| Database Status | `docker logs cloudarmor-db` | No critical DB errors |
| Firewall | `sudo ufw status` | Only required ports allowed |
| Backup | Run backup script | Backup file is created |

---

## Troubleshooting

### Nextcloud Trusted Domain Error

If Nextcloud rejects the domain, update trusted domains:

```bash
docker exec -it cloudarmor-nextcloud php occ config:system:set trusted_domains 1 --value=<DOMAIN_NAME>
```

---

### Nginx 502 Bad Gateway

Possible causes:

- Nextcloud container is down
- Nginx upstream name is incorrect
- Containers are not on the same Docker network
- Application has not fully started

Useful commands:

```bash
docker ps
docker logs cloudarmor-nginx
docker logs cloudarmor-nextcloud
docker network ls
```

---

### Database Connection Failure

Possible causes:

- Incorrect database credentials
- MariaDB container is not running
- Nextcloud and MariaDB are not on the same Docker network
- Database initialization is still in progress

Useful commands:

```bash
docker logs cloudarmor-db
docker logs cloudarmor-nextcloud
docker inspect cloudarmor-db
```

---

### SSL Certificate Issue

Possible causes:

- DNS record is incorrect
- Port 80 is blocked
- Certificate path is misconfigured
- Reverse proxy configuration is incorrect

Useful commands:

```bash
curl -I http://<DOMAIN_NAME>
curl -I https://<DOMAIN_NAME>
sudo ufw status verbose
docker logs cloudarmor-nginx
```

---

## Recommended Screenshots

Add screenshots to the `screenshots/` folder and reference them in this README.

Suggested screenshots:

- Cloud VM dashboard
- DNS A record configuration
- SSH hardening configuration
- UFW status output
- Docker containers running
- Nginx reverse proxy configuration
- Browser showing HTTPS lock
- Nextcloud login page
- Nextcloud dashboard
- Successful file upload
- MariaDB container status
- Backup script output

Example Markdown:

```markdown
![Nextcloud Login](screenshots/nextcloud-login.png)
![UFW Status](screenshots/ufw-status.png)
![Docker Containers](screenshots/docker-containers.png)
```

---

## Lessons Learned

This project reinforced that cloud infrastructure is more than launching a server. A working deployment requires multiple layers to function together correctly:

- DNS must point to the correct server
- Firewall rules must allow only required traffic
- Nginx must route requests to the correct backend
- SSL/TLS must be configured correctly
- Containers must communicate on the right networks
- The database must persist application state
- Backups must be created and tested
- SSH access must remain secure but recoverable

The project provided hands-on experience with real infrastructure challenges involving Linux, Docker, cloud networking, reverse proxying, SSL, databases, and security hardening.

---

## Future Improvements

Planned improvements include:

- Add Fail2ban for SSH protection
- Add Uptime Kuma for service monitoring
- Add Prometheus and Grafana for metrics
- Add automated offsite backups
- Add container image vulnerability scanning
- Add Terraform for infrastructure provisioning
- Add GitHub Actions for CI/CD validation
- Add Docker image version pinning
- Add managed database option
- Add cloud object storage for backups
- Add restore testing documentation
- Add centralized logging

---

## Resume Summary

**CloudArmor – Secure Cloud Infrastructure Capstone**

- Deployed a secure cloud-hosted Nextcloud collaboration platform on a Linux VM using Docker, MariaDB, Nginx reverse proxying, DNS configuration, and SSL/TLS encryption.
- Hardened server access with SSH security controls and UFW firewall rules to restrict inbound traffic to required administrative and web services.
- Implemented MariaDB as the application database backend and created backup procedures to support data recovery and operational continuity.
- Documented architecture, deployment workflow, security controls, backup process, troubleshooting steps, and validation testing for a full-stack infrastructure capstone project.

---

## Interview Explanation

> CloudArmor is a secure cloud infrastructure capstone where I deployed a self-hosted Nextcloud collaboration platform on a Linux-based cloud VM. The stack used Docker, MariaDB, Nginx reverse proxying, DNS routing, SSL/TLS encryption, UFW firewall rules, SSH hardening, and backup scripting.
>
> The goal was not just to make the application work, but to build it like a real infrastructure project. I had to think through public access, secure remote administration, reverse proxy routing, database persistence, firewall exposure, and recovery planning.
>
> The biggest lesson was understanding how each layer depends on the others. DNS had to point to the server correctly, SSL depended on the domain resolving, Nginx had to route traffic to the right container, MariaDB had to remain available for Nextcloud, and the firewall had to allow only the correct traffic. This gave me practical experience with cloud hosting, Linux administration, Docker, networking, and security controls.

---

## Disclaimer

This repository is a sanitized portfolio version of the CloudArmor capstone project. Real credentials, private keys, public IP addresses, passwords, and environment-specific secrets have been removed or replaced with placeholders.
