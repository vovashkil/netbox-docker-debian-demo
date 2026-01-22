# NetBox Docker Demo on Debian

This repository provides **step-by-step instructions** to run a **NetBox demo environment** on a Debian-based system using `netbox-docker` and `netbox-demo-data`.

The goal is to offer a simple, reproducible setup suitable for demos, learning, or evaluation.

---

## Overview

* OS: Debian (tested on Debian 11/12)
* Deployment method: Docker & Docker Compose
* NetBox source: `netbox-docker`
* Sample data: `netbox-demo-data`
* Installation path: `/opt/netbox`

Directory layout:

```
/opt/netbox/
├── netbox-docker/
└── netbox-demo-data/
```

---

## Prerequisites

Ensure the following are installed on your Debian system:

* Debian 11 or newer
* Root or sudo access
* Docker
* Docker Compose plugin

Optional but recommended:

* Git
* curl

---

## Step 1: Prepare the System

Update the system and install required packages:

```bash
apt update && apt upgrade -y
apt install -y ca-certificates curl gnupg git
```

Install Docker:

```bash
curl -fsSL https://get.docker.com | sh
systemctl enable docker --now
```

Install Docker Compose plugin:

```bash
apt install -y docker-compose-plugin
```

Verify installation:

```bash
docker --version
docker compose version
```

---

## Step 2: Create NetBox Directory

Create the base directory:

```bash
mkdir -p /opt/netbox
cd /opt/netbox
```

---

## Step 3: Clone Repositories

Clone `netbox-docker`:

```bash
git clone https://github.com/netbox-community/netbox-docker.git
```

Clone `netbox-demo-data`:

```bash
git clone https://github.com/netbox-community/netbox-demo-data.git
```

---

## Step 4: Configure NetBox Docker

Navigate to the NetBox Docker directory:

```bash
cd /opt/netbox/netbox-docker
```

Copy the example environment file:

```bash
cp env-example .env
```

Edit `.env` as needed (minimum configuration for demo):

* Database credentials
* Superuser password
* Allowed hosts

---

## Step 5: Start NetBox

Start NetBox using Docker Compose:

```bash
docker compose pull
docker compose up -d
```

Check container status:

```bash
docker compose ps
```

---

## Step 6: Access NetBox

Once containers are running, access NetBox in your browser:

```
http://<server-ip>:8000
```

Default credentials (if configured):

* Username: `admin`
* Password: defined in `.env`

---

## Step 7: Load Demo Data (Optional)

To load demo data:

```bash
cd /opt/netbox/netbox-demo-data
```

Follow the instructions in the `netbox-demo-data` repository to import sample objects into NetBox.

---

## Useful Commands

Stop NetBox:

```bash
docker compose down
```

View logs:

```bash
docker compose logs -f
```

Restart services:

```bash
docker compose restart
```

---

## Cleanup

To remove containers and volumes:

```bash
docker compose down -v
```

---

## Notes

* This setup is intended for **demo and learning purposes only**
* Not hardened for production use
* No HTTPS by default

---

## References

* NetBox: https://github.com/netbox-community/netbox
* NetBox Docker: https://github.com/netbox-community/netbox-docker
* NetBox Demo Data: https://github.com/netbox-community/netbox-demo-data

