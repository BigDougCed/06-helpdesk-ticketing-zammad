# 1 – Install Zammad with Docker

Zammad provides an official Docker Compose stack (Zammad, PostgreSQL, Elasticsearch, Redis, Memcached, Nginx). I used it unchanged instead of writing my own, because that's what you would do in a real company: use the supported deployment.

## Requirements
- Ubuntu Server 24.04 (LNX01 from project 01) with **at least 4 GB RAM** (Elasticsearch needs it; raise LNX01 from 2 GB with `Set-VMMemory LNX01 -StartupBytes 4GB -MaximumBytes 6GB` while the VM is off), or Docker Desktop on Windows
- Docker Engine + Compose plugin

## Install Docker (Ubuntu)
```bash
sudo apt update && sudo apt install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo $VERSION_CODENAME) stable" | sudo tee /etc/apt/sources.list.d/docker.list
sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo usermod -aG docker $USER   # log out and back in
```

## Elasticsearch kernel setting
```bash
echo 'vm.max_map_count=262144' | sudo tee /etc/sysctl.d/99-elasticsearch.conf
sudo sysctl --system
```

## Start Zammad
```bash
git clone https://github.com/zammad/zammad-docker-compose.git
cd zammad-docker-compose
docker compose up -d
docker compose ps          # wait until all containers are "healthy"/"running" (first start: several minutes)
```
Open `http://<LNX01-IP>:8080` and run the setup wizard: organisation *NordLicht Handel GmbH*, admin account, e-mail channel (optional: a test mailbox from project 04).

## Backup
The stack includes a backup container that writes database + attachments to a volume. It's included in the backup concept of [project 09](https://github.com/BigDougCed/09-linux-monitoring-backup).

## Update
```bash
cd zammad-docker-compose && git pull && docker compose pull && docker compose up -d
```

📸 `screenshots/docker-ps.png`, `screenshots/setup-wizard.png`
