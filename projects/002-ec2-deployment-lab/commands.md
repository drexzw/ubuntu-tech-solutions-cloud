# Ubuntu Tech Solutions - EC2 Deployment Commands

## AWS / SSH Commands

### Connect to EC2 Instance

```bash
ssh -i key-name.pem ubuntu@public-ip-address
System Updates

Update package lists:

sudo apt update

Upgrade installed packages:

sudo apt upgrade -y
Apache Installation

Install Apache Web Server:

sudo apt install apache2 -y

Check Apache status:

sudo systemctl status apache2

Restart Apache:

sudo systemctl restart apache2
Website Management

Navigate to website directory:

cd /var/www/html

Edit website:

sudo nano index.html
System Information Commands

Check operating system:

cat /etc/os-release

Check disk usage:

df -h

Check memory usage:

free -h

Check server uptime:

uptime
Networking Commands

View listening ports:

ss -tulnp

Check public IP:

curl ifconfig.me
Service Management

Start service:

sudo systemctl start apache2

Stop service:

sudo systemctl stop apache2

Enable Apache on startup:

sudo systemctl enable apache2
