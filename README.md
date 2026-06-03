# Technitium DNS Deployment

This repository contains the deployment configuration for Technitium DNS, which serves as the local authoritative DNS server and forwarder for the homelab.

## Prerequisites & Pre-Flight Check

Before deploying, ensure that a Docker host is available and that **Port 53 (TCP/UDP) is completely free**. 

Run the following command on the host to check if any service is currently using port 53:
```bash
sudo ss -tulpn | grep :53
```

- **If there is no output:** Port 53 is free. You can proceed with the deployment.
- **If there is output (e.g., `systemd-resolved`):** You must disable the local stub resolver before starting the container. Run the following commands to free up the port:

```bash
sudo systemctl disable --now systemd-resolved
sudo rm /etc/resolv.conf
echo "nameserver 1.1.1.1" | sudo tee /etc/resolv.conf
```

## Deployment

1. Prepare the deployment directory:
```bash
sudo mkdir -p /opt/technitium
sudo chown $USER:$USER /opt/technitium
cd /opt/technitium

# Clone this repository
git clone git@github.com:sdrwtf-labs/technitium-deployment.git .
```

2. Configure the environment:

```bash
cp .env.example .env
```

*(Edit the `.env` file if you need to change the default Web UI port).*

3. Start the DNS server:

```bash
docker compose up -d
```

## Post-Deployment

Access the Web UI at `http://<host-ip>:5380`.

During the initial setup, you will be prompted to create an admin password. After logging in:

1. Configure forwarding to your preferred upstream DNS (e.g., 1.1.1.1, 8.8.8.8, or your ISP router).
2. Create an authoritative zone for the local infrastructure.
