# Portainer Installation (Ubuntu & RHEL)

## Step 1: Verify Docker

```bash
docker --version
sudo systemctl status docker
```

---

## Step 2: Create a Docker Volume

```bash
docker volume create portainer_data
```

---

## Step 3: Run Portainer

```bash
docker run -d \
  --name portainer \
  --restart=always \
  -p 9000:9000 \
  -p 9443:9443 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

---

## Step 4: Verify Portainer Container

```bash
docker ps
```

---

## Step 5: Open Firewall

### Ubuntu

```bash
sudo ufw allow 9443/tcp
sudo ufw reload
```

### RHEL / Rocky / AlmaLinux

```bash
sudo firewall-cmd --permanent --add-port=9443/tcp
sudo firewall-cmd --reload
```

---

## Step 6: Access Portainer

```
https://<SERVER-IP>:9443
```

---

## Step 7: Create Administrator Account

- Create username
- Create password
- Click **Create User**

---

## Step 8: Connect to Docker

- Select **Get Started**
- Choose **Local**
- Click **Connect**