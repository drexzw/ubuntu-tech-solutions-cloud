# Ubuntu Tech Solutions — Load Balancer Lab Commands

## Apache Installation

Run on each Ubuntu web server:

```bash
sudo apt update
sudo apt install apache2 -y
```

## Check Apache Status

```bash
sudo systemctl status apache2
```

Expected state:

```text
Active: active (running)
```

## Test Apache Locally

```bash
curl http://localhost
```

This confirms that Apache is responding locally before testing the load balancer.

## Configure Web Server 01

```bash
echo '<html><body><h1>Ubuntu Tech Solutions - Web Server 01</h1><p>Served by EC2 Instance 01</p></body></html>' | sudo tee /var/www/html/index.html
```

## Configure Web Server 02

```bash
echo '<html><body><h1>Ubuntu Tech Solutions - Web Server 02</h1><p>Served by EC2 Instance 02</p></body></html>' | sudo tee /var/www/html/index.html
```

## Stop Apache for Failure Testing

On Web Server 01:

```bash
sudo systemctl stop apache2
```

Verify:

```bash
sudo systemctl status apache2
```

Expected state:

```text
inactive (dead)
```

## Restore Apache

```bash
sudo systemctl start apache2
```

Verify:

```bash
sudo systemctl status apache2
```

Expected state:

```text
active (running)
```

## Test Web Server 01

```bash
curl http://localhost
```

Expected result contains:

```text
Ubuntu Tech Solutions - Web Server 01
```

## Test Web Server 02

```bash
curl http://localhost
```

Expected result contains:

```text
Ubuntu Tech Solutions - Web Server 02
```

## AWS Configuration

The remaining configuration was performed through the AWS Management Console:

* VPC
* Internet Gateway
* Subnets
* Route Table
* Security Groups
* EC2
* Target Group
* Application Load Balancer
* Listener
* Health Checks
