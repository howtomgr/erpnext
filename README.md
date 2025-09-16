# erpnext Installation Guide

erpnext is a free and open-source enterprise resource planning. ERPNext provides full-featured ERP system built on Frappe

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
  - RAM: 4GB minimum
  - Storage: 20GB for data
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default erpnext port)
  - Various service ports
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

# Install erpnext
sudo dnf install -y erpnext

# Enable and start service
sudo systemctl enable --now erpnext

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
erpnext --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install erpnext
sudo apt install -y erpnext

# Enable and start service
sudo systemctl enable --now erpnext

# Configure firewall
sudo ufw allow 80

# Verify installation
erpnext --version
```

### Arch Linux

```bash
# Install erpnext
sudo pacman -S erpnext

# Enable and start service
sudo systemctl enable --now erpnext

# Verify installation
erpnext --version
```

### Alpine Linux

```bash
# Install erpnext
apk add --no-cache erpnext

# Enable and start service
rc-update add erpnext default
rc-service erpnext start

# Verify installation
erpnext --version
```

### openSUSE/SLES

```bash
# Install erpnext
sudo zypper install -y erpnext

# Enable and start service
sudo systemctl enable --now erpnext

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
erpnext --version
```

### macOS

```bash
# Using Homebrew
brew install erpnext

# Start service
brew services start erpnext

# Verify installation
erpnext --version
```

### FreeBSD

```bash
# Using pkg
pkg install erpnext

# Enable in rc.conf
echo 'erpnext_enable="YES"' >> /etc/rc.conf

# Start service
service erpnext start

# Verify installation
erpnext --version
```

### Windows

```bash
# Using Chocolatey
choco install erpnext

# Or using Scoop
scoop install erpnext

# Verify installation
erpnext --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/erpnext

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
erpnext --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable erpnext

# Start service
sudo systemctl start erpnext

# Stop service
sudo systemctl stop erpnext

# Restart service
sudo systemctl restart erpnext

# Check status
sudo systemctl status erpnext

# View logs
sudo journalctl -u erpnext -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add erpnext default

# Start service
rc-service erpnext start

# Stop service
rc-service erpnext stop

# Restart service
rc-service erpnext restart

# Check status
rc-service erpnext status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'erpnext_enable="YES"' >> /etc/rc.conf

# Start service
service erpnext start

# Stop service
service erpnext stop

# Restart service
service erpnext restart

# Check status
service erpnext status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start erpnext
brew services stop erpnext
brew services restart erpnext

# Check status
brew services list | grep erpnext
```

### Windows Service Manager

```powershell
# Start service
net start erpnext

# Stop service
net stop erpnext

# Using PowerShell
Start-Service erpnext
Stop-Service erpnext
Restart-Service erpnext

# Check status
Get-Service erpnext
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream erpnext_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name erpnext.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name erpnext.example.com;

    ssl_certificate /etc/ssl/certs/erpnext.example.com.crt;
    ssl_certificate_key /etc/ssl/private/erpnext.example.com.key;

    location / {
        proxy_pass http://erpnext_backend;
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
    ServerName erpnext.example.com
    Redirect permanent / https://erpnext.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName erpnext.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/erpnext.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/erpnext.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend erpnext_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/erpnext.pem
    redirect scheme https if !{ ssl_fc }
    default_backend erpnext_backend

backend erpnext_backend
    balance roundrobin
    server erpnext1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R erpnext:erpnext /etc/erpnext
sudo chmod 750 /etc/erpnext

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
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
sudo systemctl status erpnext

# View logs
sudo journalctl -u erpnext -f

# Monitor resource usage
top -p $(pgrep erpnext)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/erpnext"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/erpnext-backup-$DATE.tar.gz" /etc/erpnext /var/lib/erpnext

echo "Backup completed: $BACKUP_DIR/erpnext-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop erpnext

# Restore from backup
tar -xzf /backup/erpnext/erpnext-backup-*.tar.gz -C /

# Start service
sudo systemctl start erpnext
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u erpnext -n 100
sudo tail -f /var/log/erpnext/erpnext.log

# Check configuration
erpnext --version

# Check permissions
ls -la /etc/erpnext
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep erpnext)

# Check disk I/O
iotop -p $(pgrep erpnext)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  erpnext:
    image: erpnext:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/erpnext
      - ./data:/var/lib/erpnext
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update erpnext

# Debian/Ubuntu
sudo apt update && sudo apt upgrade erpnext

# Arch Linux
sudo pacman -Syu erpnext

# Alpine Linux
apk update && apk upgrade erpnext

# openSUSE
sudo zypper update erpnext

# FreeBSD
pkg update && pkg upgrade erpnext

# Always backup before updates
tar -czf /backup/erpnext-pre-update-$(date +%Y%m%d).tar.gz /etc/erpnext

# Restart after updates
sudo systemctl restart erpnext
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/erpnext

# Clean old logs
find /var/log/erpnext -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/erpnext
```

## Additional Resources

- Official Documentation: https://docs.erpnext.org/
- GitHub Repository: https://github.com/erpnext/erpnext
- Community Forum: https://forum.erpnext.org/
- Best Practices Guide: https://docs.erpnext.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
