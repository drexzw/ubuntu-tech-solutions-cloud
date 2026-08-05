<img width="1917" height="908" alt="01-instance-running" src="https://github.com/user-attachments/assets/fc78e24a-07ba-4e3f-a6e3-b6218f68da4e" />

# 2. Network Security Configuration

A Security Group was configured to control inbound traffic.

The following rules were implemented:

| Protocol | Port | Purpose |
|----------|------|---------|
| SSH | 22 | Remote server administration |
| HTTP | 80 | Web traffic access |

Evidence: <img width="1612" height="485" alt="02-sceurity-group" src="https://github.com/user-attachments/assets/ca6a46f4-2d09-4654-908c-04acc9a9bb0b" />
---

# 3. Remote Server Administration

The Ubuntu server was accessed remotely through SSH using a private key.

This demonstrated:

- Secure remote access
- Linux terminal administration
- Cloud server management

Evidence: <img width="1470" height="1032" alt="03-ssh-login" src="https://github.com/user-attachments/assets/76907617-b8f9-4f69-a429-edfc9a0140e9" />

---

# 4. Apache Web Server Configuration

Apache Web Server was installed and configured on the Ubuntu instance.

The service was verified to ensure it was running correctly.

Evidence:<img width="1312" height="582" alt="04-apache-running" src="https://github.com/user-attachments/assets/2e4ed006-b564-411f-a16a-55f6ed3033d6" />

---

# 5. Website Deployment

A custom HTML webpage was created and hosted using Apache.

The webpage confirmed that:

- The EC2 instance was reachable
- Apache was functioning correctly
- HTTP traffic was successfully configured

Evidence: <img width="1208" height="835" alt="05-webpage" src="https://github.com/user-attachments/assets/ac455bc9-1493-4d83-8031-06639eb632c1" />

---

# Verification Performed

The following Linux commands were used to verify the server:

- Operating system information
- Disk usage
- Memory usage
- Server uptime
- Network connections
- Apache service status

A full list of commands used can be found in: https://github.com/drexzw/ubuntu-tech-solutions-cloud/blob/main/projects/002-ec2-deployment-lab/commands.md






