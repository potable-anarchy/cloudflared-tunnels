# Cloudflared Tunnels Configuration

This repository contains the Cloudflare tunnel configurations and systemd services for all tunnels and applications.

## Services

### SSH Tunnel (`ssh-access`)
- **Domains**: 
  - `ssh.brad-dougherty.com` 
  - `wsl-ssh.brad-dougherty.com`
- **Service**: SSH access to local machine (port 22)
- **Config**: `ssh-config.yml`
- **Systemd Service**: `cloudflared-ssh.service`

### WSL Tunnel (`wsl-ai-gen`) 
- **Domains**:
  - `ai-image-gen.brad-dougherty.com`
  - `terminal.brad-dougherty.com`
- **Services**: 
  - AI image generation (Stable Diffusion WebUI) on port 7860
  - Terminal access on port 4200
- **Config**: `wsl-config.yml`
- **Systemd Service**: `cloudflared-wsl.service`

## Configuration Breakdown

### SSH Config (`ssh-config.yml`)
```yaml
tunnel: 5ec6b36f-879a-41e4-b90e-17ad6aab6a4f
credentials-file: /home/brad/.cloudflared/5ec6b36f-879a-41e4-b90e-17ad6aab6a4f.json

ingress:
  - hostname: ssh.brad-dougherty.com
    service: ssh://127.0.0.1:22
  - hostname: wsl-ssh.brad-dougherty.com
    service: ssh://127.0.0.1:22
  - service: http_status:404
```

### WSL Config (`wsl-config.yml`)
```yaml
tunnel: 7c46021c-2ea9-41d5-bd3f-09f81ce9530e
credentials-file: /home/brad/.cloudflared/7c46021c-2ea9-41d5-bd3f-09f81ce9530e.json

ingress:
  - hostname: ai-image-gen.brad-dougherty.com
    service: http://127.0.0.1:7860
  - hostname: terminal.brad-dougherty.com
    service: http://127.0.0.1:4200
  - service: http_status:404
```

## Systemd Services

Both services are configured for:
- **Auto-start**: Start on system boot
- **Auto-restart**: Restart on failure (`Restart=always`)
- **User**: Run as `brad` user
- **Logging**: Output to systemd journal

### Managing Services

```bash
# Check status
systemctl status cloudflared-ssh.service cloudflared-wsl.service

# Start services
sudo systemctl start cloudflared-ssh.service cloudflared-wsl.service

# Stop services  
sudo systemctl stop cloudflared-ssh.service cloudflared-wsl.service

# Restart services
sudo systemctl restart cloudflared-ssh.service cloudflared-wsl.service

# View logs
journalctl -u cloudflared-ssh.service -f
journalctl -u cloudflared-wsl.service -f
```

## Installation

1. **Install cloudflared**:
```bash
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
```

2. **Copy configs**:
```bash
sudo cp ssh-config.yml /home/brad/.cloudflared/
sudo cp wsl-config.yml /home/brad/.cloudflared/
```

3. **Install systemd services**:
```bash
sudo cp cloudflared-ssh.service /etc/systemd/system/
sudo cp cloudflared-wsl.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable cloudflared-ssh.service cloudflared-wsl.service
```

4. **Start services**:
```bash
sudo systemctl start cloudflared-ssh.service cloudflared-wsl.service
```

## Tunnel IDs

- **SSH Tunnel**: `5ec6b36f-879a-41e4-b90e-17ad6aab6a4f`
- **WSL Tunnel**: `7c46021c-2ea9-41d5-bd3f-09f81ce9530e`

## Security Notes

- Credential files contain sensitive tunnel tokens and should never be committed to version control
- SSH access is available through both domain endpoints
- AI image gen service is protected by Cloudflare Access authentication
- All services run with auto-restart for reliability

## All Tunnels Overview

### Active Tunnels (with configurations in this repo)

| Tunnel Name | ID | Status | Domains | Purpose |
|-------------|-----|--------|---------|---------|
| **ssh-access** | `5ec6b36f-879a-41e4-b90e-17ad6aab6a4f` | ✅ Active (2 connections) | `ssh.brad-dougherty.com`<br>`wsl-ssh.brad-dougherty.com` | SSH access to local machine |
| **wsl-ai-gen** | `7c46021c-2ea9-41d5-bd3f-09f81ce9530e` | ✅ Active (2 connections) | `ai-image-gen.brad-dougherty.com`<br>`terminal.brad-dougherty.com` | AI image generation & terminal |

### Other Tunnels (configurations not tracked here yet)

| Tunnel Name | ID | Status | Purpose |
|-------------|-----|--------|---------|
| **cascade-webhook** | `4158f6cf-fbaf-4980-85c7-0b4aa00d4657` | ✅ Active (4 connections) | Cascade webhook service |
| **cascadeadventures-legacy** | `a7577b7a-4f80-4d66-8e8a-24821b9949ed` | ✅ Active (4 connections) | Legacy Cascade Adventures site |
| **cascadeadventures.net** | `107da296-cabd-48c9-b06e-225a0d7c47bb` | ⚠️ Inactive | Main Cascade Adventures site |
| **coder-server** | `d83cf254-dc78-4282-a739-5e383f4eb1fd` | ✅ Active (4 connections) | Code server/development environment |
| **coolify** | `b3c8a584-acd6-4dfc-a568-2c6429a8042f` | ⚠️ Inactive | Coolify deployment platform |
| **hire-test** | `6db913bb-e97c-4ef2-a7a4-bf06e34471f2` | ⚠️ Inactive | Hiring/testing application |
| **prod-pi** | `187dc599-a1c8-48d0-84aa-19fa622ccd3a` | ⚠️ Inactive | Production Raspberry Pi services |
| **resume.brad-dougherty.com** | `7ec98ccc-054e-4465-92aa-6c596a87fa38` | ⚠️ Inactive | Personal resume site |

## Live URLs

- **SSH Access**: `ssh.brad-dougherty.com`, `wsl-ssh.brad-dougherty.com`
- **AI Image Gen**: `ai-image-gen.brad-dougherty.com` 
- **Terminal**: `terminal.brad-dougherty.com`