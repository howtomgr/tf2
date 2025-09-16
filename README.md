# tf2 Installation Guide

tf2 is a free and open-source Team Fortress 2 server. Team Fortress 2 dedicated server for multiplayer matches

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 2+ cores
  - RAM: 2GB minimum
  - Storage: 20GB for game
  - Network: Source protocol
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 27015 (default tf2 port)
  - RCON available
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install tf2
sudo dnf install -y tf2

# Enable and start service
sudo systemctl enable --now tf2

# Configure firewall
sudo firewall-cmd --permanent --add-port=27015/tcp
sudo firewall-cmd --reload

# Verify installation
tf2 --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install tf2
sudo apt install -y tf2

# Enable and start service
sudo systemctl enable --now tf2

# Configure firewall
sudo ufw allow 27015

# Verify installation
tf2 --version
```

### Arch Linux

```bash
# Install tf2
sudo pacman -S tf2

# Enable and start service
sudo systemctl enable --now tf2

# Verify installation
tf2 --version
```

### Alpine Linux

```bash
# Install tf2
apk add --no-cache tf2

# Enable and start service
rc-update add tf2 default
rc-service tf2 start

# Verify installation
tf2 --version
```

### openSUSE/SLES

```bash
# Install tf2
sudo zypper install -y tf2

# Enable and start service
sudo systemctl enable --now tf2

# Configure firewall
sudo firewall-cmd --permanent --add-port=27015/tcp
sudo firewall-cmd --reload

# Verify installation
tf2 --version
```

### macOS

```bash
# Using Homebrew
brew install tf2

# Start service
brew services start tf2

# Verify installation
tf2 --version
```

### FreeBSD

```bash
# Using pkg
pkg install tf2

# Enable in rc.conf
echo 'tf2_enable="YES"' >> /etc/rc.conf

# Start service
service tf2 start

# Verify installation
tf2 --version
```

### Windows

```bash
# Using Chocolatey
choco install tf2

# Or using Scoop
scoop install tf2

# Verify installation
tf2 --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/tf2

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
tf2 --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable tf2

# Start service
sudo systemctl start tf2

# Stop service
sudo systemctl stop tf2

# Restart service
sudo systemctl restart tf2

# Check status
sudo systemctl status tf2

# View logs
sudo journalctl -u tf2 -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add tf2 default

# Start service
rc-service tf2 start

# Stop service
rc-service tf2 stop

# Restart service
rc-service tf2 restart

# Check status
rc-service tf2 status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'tf2_enable="YES"' >> /etc/rc.conf

# Start service
service tf2 start

# Stop service
service tf2 stop

# Restart service
service tf2 restart

# Check status
service tf2 status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start tf2
brew services stop tf2
brew services restart tf2

# Check status
brew services list | grep tf2
```

### Windows Service Manager

```powershell
# Start service
net start tf2

# Stop service
net stop tf2

# Using PowerShell
Start-Service tf2
Stop-Service tf2
Restart-Service tf2

# Check status
Get-Service tf2
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream tf2_backend {
    server 127.0.0.1:27015;
}

server {
    listen 80;
    server_name tf2.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name tf2.example.com;

    ssl_certificate /etc/ssl/certs/tf2.example.com.crt;
    ssl_certificate_key /etc/ssl/private/tf2.example.com.key;

    location / {
        proxy_pass http://tf2_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName tf2.example.com
    Redirect permanent / https://tf2.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName tf2.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/tf2.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/tf2.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:27015/
    ProxyPassReverse / http://127.0.0.1:27015/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend tf2_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/tf2.pem
    redirect scheme https if !{ ssl_fc }
    default_backend tf2_backend

backend tf2_backend
    balance roundrobin
    server tf21 127.0.0.1:27015 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R tf2:tf2 /etc/tf2
sudo chmod 750 /etc/tf2

# Configure firewall
sudo firewall-cmd --permanent --add-port=27015/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status tf2

# View logs
sudo journalctl -u tf2 -f

# Monitor resource usage
top -p $(pgrep tf2)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/tf2"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/tf2-backup-$DATE.tar.gz" /etc/tf2 /var/lib/tf2

echo "Backup completed: $BACKUP_DIR/tf2-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop tf2

# Restore from backup
tar -xzf /backup/tf2/tf2-backup-*.tar.gz -C /

# Start service
sudo systemctl start tf2
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u tf2 -n 100
sudo tail -f /var/log/tf2/tf2.log

# Check configuration
tf2 --version

# Check permissions
ls -la /etc/tf2
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 27015

# Test connectivity
telnet localhost 27015

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep tf2)

# Check disk I/O
iotop -p $(pgrep tf2)

# Check connections
ss -an | grep 27015
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  tf2:
    image: tf2:latest
    ports:
      - "27015:27015"
    volumes:
      - ./config:/etc/tf2
      - ./data:/var/lib/tf2
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update tf2

# Debian/Ubuntu
sudo apt update && sudo apt upgrade tf2

# Arch Linux
sudo pacman -Syu tf2

# Alpine Linux
apk update && apk upgrade tf2

# openSUSE
sudo zypper update tf2

# FreeBSD
pkg update && pkg upgrade tf2

# Always backup before updates
tar -czf /backup/tf2-pre-update-$(date +%Y%m%d).tar.gz /etc/tf2

# Restart after updates
sudo systemctl restart tf2
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/tf2

# Clean old logs
find /var/log/tf2 -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/tf2
```

## Additional Resources

- Official Documentation: https://docs.tf2.org/
- GitHub Repository: https://github.com/tf2/tf2
- Community Forum: https://forum.tf2.org/
- Best Practices Guide: https://docs.tf2.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
