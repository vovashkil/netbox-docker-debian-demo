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

```text
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
apt install -y ca-certificates curl gnupg git lsb-release
```

Add Docker's official GPG key:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Add the repository to Apt sources:

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

Install the Docker packages (latest version):

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verify/Start docker service:

```bash
sudo systemctl status docker
sudo systemctl start docker
```

Add your current user to group docker to run `docker` commands without sudo. This readme is written for a demo on an EC2 Instance, and System Manager Connect is used to connect under user `ssm-user`

```bash
sudo usermod -aG docker ssm-user
```

Verify installation:

```bash
docker --version
docker compose version
```

Verify that the installation is successful by running the hello-world image:

```bash
sudo docker run hello-world
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

Copy the example override file:

```bash
cp docker-compose.override.yml.example docker-compose.override.yml
```

Read and edit the file to your liking.

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

```text
http://<server-ip>:8000
```

---

## Step 7: Load Demo Data (Optional)

Stop netbox and netbox-worker:

```bash
docker compose stop netbox netbox-worker
```

Load demo-data to the database:

```bash
# re-crete the database
docker compose exec postgres sh -c 'psql -U $POSTGRES_USER postgres -c "DROP DATABASE netbox;"'
docker compose exec postgres sh -c 'psql -U $POSTGRES_USER postgres -c "CREATE DATABASE netbox;"'
# copy data inside the container
docker cp ../netbox-demo-data/sql/netbox-demo-v4.5.sql  "$(docker compose ps -q postgres)":/tmp/netbox-demo.sql
# load data to the database
docker compose exec postgres  bash -c "psql -U $POSTGRES_USER netbox < /tmp/netbox-demo.sql"
# restart netbox
docker compose up -d
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
