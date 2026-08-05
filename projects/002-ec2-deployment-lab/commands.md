# Ubuntu Tech Solutions - EC2 Deployment Commands

## AWS / SSH Commands

### Connect to EC2 Instance

```bash
ssh -i key-name.pem ubuntu@public-ip-address
```

---

## System Updates

**Update package lists:**

```bash
sudo apt update
```

**Upgrade installed packages:**

```bash
sudo apt upgrade -y
```

---

## Apache Installation

**Install Apache Web Server:**

```bash
sudo apt install apache2 -y
```

**Check Apache status:**

```bash
sudo systemctl status apache2
```

**Restart Apache:**

```bash
sudo systemctl restart apache2
```

---

## Website Management

**Navigate to website directory:**

```bash
cd /var/www/html
```

**Edit website:**

```bash
sudo nano index.html
```

---

## System Information Commands

**Check operating system:**

```bash
cat /etc/os-release
```

**Check disk usage:**

```bash
df -h
```

**Check memory usage:**

```bash
free -h
```

**Check server uptime:**

```bash
uptime
```

---

## Networking Commands

**View listening ports:**

```bash
ss -tulnp
```

**Check public IP:**

```bash
curl ifconfig.me
```

---

## Service Management

**Start service:**

```bash
sudo systemctl start apache2
```

**Stop service:**

```bash
sudo systemctl stop apache2
```

**Enable Apache on startup:**

```bash
sudo systemctl enable apache2
```
