# navidrome Installation Guide

navidrome is a free and open-source modern music server. Navidrome provides a modern, lightweight music server and streamer compatible with Subsonic/Airsonic

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
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 10GB+ for music
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 4533 (default navidrome port)
  - None
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

# Install navidrome
sudo dnf install -y navidrome

# Enable and start service
sudo systemctl enable --now navidrome

# Configure firewall
sudo firewall-cmd --permanent --add-port=4533/tcp
sudo firewall-cmd --reload

# Verify installation
navidrome --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install navidrome
sudo apt install -y navidrome

# Enable and start service
sudo systemctl enable --now navidrome

# Configure firewall
sudo ufw allow 4533

# Verify installation
navidrome --version
```

### Arch Linux

```bash
# Install navidrome
sudo pacman -S navidrome

# Enable and start service
sudo systemctl enable --now navidrome

# Verify installation
navidrome --version
```

### Alpine Linux

```bash
# Install navidrome
apk add --no-cache navidrome

# Enable and start service
rc-update add navidrome default
rc-service navidrome start

# Verify installation
navidrome --version
```

### openSUSE/SLES

```bash
# Install navidrome
sudo zypper install -y navidrome

# Enable and start service
sudo systemctl enable --now navidrome

# Configure firewall
sudo firewall-cmd --permanent --add-port=4533/tcp
sudo firewall-cmd --reload

# Verify installation
navidrome --version
```

### macOS

```bash
# Using Homebrew
brew install navidrome

# Start service
brew services start navidrome

# Verify installation
navidrome --version
```

### FreeBSD

```bash
# Using pkg
pkg install navidrome

# Enable in rc.conf
echo 'navidrome_enable="YES"' >> /etc/rc.conf

# Start service
service navidrome start

# Verify installation
navidrome --version
```

### Windows

```bash
# Using Chocolatey
choco install navidrome

# Or using Scoop
scoop install navidrome

# Verify installation
navidrome --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/navidrome

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
navidrome --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable navidrome

# Start service
sudo systemctl start navidrome

# Stop service
sudo systemctl stop navidrome

# Restart service
sudo systemctl restart navidrome

# Check status
sudo systemctl status navidrome

# View logs
sudo journalctl -u navidrome -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add navidrome default

# Start service
rc-service navidrome start

# Stop service
rc-service navidrome stop

# Restart service
rc-service navidrome restart

# Check status
rc-service navidrome status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'navidrome_enable="YES"' >> /etc/rc.conf

# Start service
service navidrome start

# Stop service
service navidrome stop

# Restart service
service navidrome restart

# Check status
service navidrome status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start navidrome
brew services stop navidrome
brew services restart navidrome

# Check status
brew services list | grep navidrome
```

### Windows Service Manager

```powershell
# Start service
net start navidrome

# Stop service
net stop navidrome

# Using PowerShell
Start-Service navidrome
Stop-Service navidrome
Restart-Service navidrome

# Check status
Get-Service navidrome
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream navidrome_backend {
    server 127.0.0.1:4533;
}

server {
    listen 80;
    server_name navidrome.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name navidrome.example.com;

    ssl_certificate /etc/ssl/certs/navidrome.example.com.crt;
    ssl_certificate_key /etc/ssl/private/navidrome.example.com.key;

    location / {
        proxy_pass http://navidrome_backend;
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
    ServerName navidrome.example.com
    Redirect permanent / https://navidrome.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName navidrome.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/navidrome.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/navidrome.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:4533/
    ProxyPassReverse / http://127.0.0.1:4533/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend navidrome_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/navidrome.pem
    redirect scheme https if !{ ssl_fc }
    default_backend navidrome_backend

backend navidrome_backend
    balance roundrobin
    server navidrome1 127.0.0.1:4533 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R navidrome:navidrome /etc/navidrome
sudo chmod 750 /etc/navidrome

# Configure firewall
sudo firewall-cmd --permanent --add-port=4533/tcp
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
sudo systemctl status navidrome

# View logs
sudo journalctl -u navidrome -f

# Monitor resource usage
top -p $(pgrep navidrome)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/navidrome"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/navidrome-backup-$DATE.tar.gz" /etc/navidrome /var/lib/navidrome

echo "Backup completed: $BACKUP_DIR/navidrome-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop navidrome

# Restore from backup
tar -xzf /backup/navidrome/navidrome-backup-*.tar.gz -C /

# Start service
sudo systemctl start navidrome
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u navidrome -n 100
sudo tail -f /var/log/navidrome/navidrome.log

# Check configuration
navidrome --version

# Check permissions
ls -la /etc/navidrome
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 4533

# Test connectivity
telnet localhost 4533

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep navidrome)

# Check disk I/O
iotop -p $(pgrep navidrome)

# Check connections
ss -an | grep 4533
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  navidrome:
    image: navidrome:latest
    ports:
      - "4533:4533"
    volumes:
      - ./config:/etc/navidrome
      - ./data:/var/lib/navidrome
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update navidrome

# Debian/Ubuntu
sudo apt update && sudo apt upgrade navidrome

# Arch Linux
sudo pacman -Syu navidrome

# Alpine Linux
apk update && apk upgrade navidrome

# openSUSE
sudo zypper update navidrome

# FreeBSD
pkg update && pkg upgrade navidrome

# Always backup before updates
tar -czf /backup/navidrome-pre-update-$(date +%Y%m%d).tar.gz /etc/navidrome

# Restart after updates
sudo systemctl restart navidrome
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/navidrome

# Clean old logs
find /var/log/navidrome -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/navidrome
```

## Additional Resources

- Official Documentation: https://docs.navidrome.org/
- GitHub Repository: https://github.com/navidrome/navidrome
- Community Forum: https://forum.navidrome.org/
- Best Practices Guide: https://docs.navidrome.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
