# Appwrite Docker Installation Guide
### For Low-Bandwidth / Unstable Internet Environments

This guide provides a step-by-step installation process for Appwrite using Docker Compose, optimized for slow or unstable internet connections. Images are pulled individually to prevent download failures and corruption.

## Prerequisites
- Docker Engine installed and running
- Docker Compose (v2+) installed
- Minimum ~5 Mbps consistent internet connection recommended for `docker compose up`
- Ports `20080` and `20443` available on the host machine
---

## Docker Compose Configuration
Create or update your `docker-compose.yml` with the following port mappings:

```yaml
services:
  # ... your existing Appwrite services ...
  
  traefik:
    ports:
      - "20080:80"
      - "20443:443"
    # ... rest of traefik config ...
```

> **Note:** The host ports `20080` (HTTP) and `20443` (HTTPS) map to the container's standard ports `80` and `443`.

---

## Required Images

| Service | Image | Version |
|---------|-------|---------|
| Appwrite | `appwrite/appwrite` | `1.9.5` |
| Traefik | `traefik` | `3.6` |
| MariaDB | `mariadb` | `10.11` |
| Browser | `appwrite/browser` | `0.3.2` |
| Executor | `openruntimes/executor` | `0.25.1` |
| Console | `appwrite/console` | `8.7.5` |
| Redis | `redis` | `7.4.7-alpine` |

---

## Step-by-Step Installation
### Step 1: Pull Infrastructure Images (One at a Time)

Run each command individually. Wait for each to complete successfully before proceeding.

```bash
# 1. Pull Traefik (reverse proxy)
docker compose pull traefik

# 2. Pull MariaDB (database)
docker compose pull mariadb

# 3. Pull Redis (caching)
docker compose pull redis
```

### Step 2: Pull Appwrite Core Images
```bash
# 4. Pull Appwrite main service
docker compose pull appwrite

# 5. Pull remaining Appwrite services
docker compose pull appwrite-browser
docker compose pull openruntimes-executor
docker compose pull appwrite-console
```

> **Tip:** If a pull fails due to network interruption, simply re-run the same command. Docker will resume from where it left off. 

start with those four i havent found out a way that i can work with them one by one
so if you have mbps of 5mbps and above that is consistent then run
    docker compose up -d --remove-orphans
If things crash run it again
after all is done appwrite will start

## Starting the Services

Once all images are successfully pulled, start the full stack:

```bash
docker compose up -d --remove-orphans
```

### If Startup Fails

If containers crash during startup (often due to service dependencies not being ready), re-run:

```bash
docker compose up -d --remove-orphans
```

Appwrite will automatically start once all services are healthy.

## Accessing the Console

After successful installation, open your browser and navigate to:

```
http://localhost:20080/console/
```

## Lifecycle Commands

| Action | Command |
|--------|---------|
| **Start services** | `docker compose start` |
| **Stop services** | `docker compose stop` |
| **Restart services** | `docker compose restart` |
| **View logs** | `docker compose logs -f` |
| **View specific service logs** | `docker compose logs -f appwrite` |
| **Check service status** | `docker compose ps` |


## Troubleshooting

| Issue | Solution |
|-------|----------|
| Download crashes mid-pull | Re-run the same `docker compose pull <service>` command |
| Container exits immediately | Check logs: `docker compose logs <service>` |
| Port already in use | Ensure ports `20080` and `20443` are free: `lsof -i :20080` |
| Services not connecting | Wait 30–60 seconds after startup for dependency initialization |


## Quick Reference Script

For convenience, save this as `install.sh`:

```bash
#!/bin/bash
set -e

echo "=== Pulling infrastructure images ==="
docker compose pull traefik
docker compose pull mariadb
docker compose pull redis

echo "=== Pulling Appwrite images ==="
docker compose pull appwrite
docker compose pull appwrite-browser
docker compose pull openruntimes-executor
docker compose pull appwrite-console

echo "=== Starting services ==="
docker compose up -d --remove-orphans

echo "=== Installation complete ==="
echo "Access Appwrite at: http://localhost:20080/console/"
```

Make it executable:
```bash
chmod +x install.sh
./install.sh
```

---

**Last Updated:** July 2026