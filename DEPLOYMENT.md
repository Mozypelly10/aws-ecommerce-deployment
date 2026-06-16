# Deployment Log — Full Record

**Engineer:** Mozypelly
**AWS Account:** Mozytech (522196680583)
**Region:** Asia Pacific (Sydney) — ap-southeast-2
**Started:** June 13, 2026
**Phase 2 Complete:** June 16, 2026

---

## Phase 1 — Infrastructure Setup (June 13, 2026)

### AWS Account
- Created dedicated AWS account: wonzstore.admin@gmail.com
- Account name: Mozytech
- Selected Basic Support (free)
- Region set to ap-southeast-2 (Sydney)

### Security
- MFA enabled on root account (Google Authenticator)
- IAM user created: wonzystore-admin
- MFA enabled on IAM user
- AdministratorAccess policy attached
- Account alias: wonzystore
- Login URL: https://wonzystore.signin.aws.amazon.com/console

### Billing
- Zero-spend budget alert configured
- Cost Explorer activated

### EC2 Instance
```
Name:          wonzystore-webserver
AMI:           Ubuntu Server 24.04 LTS (ami-020728ad6199d7fa0)
Instance type: t2.micro
Storage:       20 GiB gp3
Key pair:      wonzystore-keypair.pem
Instance ID:   i-0f4706a0d42cb33f7
Public IP:     3.107.29.242
Private IP:    172.31.0.72
```

### Key pair setup (Mac)
```bash
mkdir -p ~/.ssh
mv ~/Downloads/wonzystore-keypair.pem ~/.ssh/
chmod 400 ~/.ssh/wonzystore-keypair.pem
```

### Security Group — wonzystore-sg
```
SSH    TCP  22    Admin IPs only
HTTP   TCP  80    0.0.0.0/0
HTTPS  TCP  443   0.0.0.0/0
```

### Server Setup
```bash
ssh -i ~/.ssh/wonzystore-keypair.pem ubuntu@3.107.29.242
sudo apt update && sudo apt upgrade -y
sudo apt install nginx -y
sudo systemctl enable nginx
sudo apt install ufw -y
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

---

## Phase 2 — Application Deployment (June 14-16, 2026)

### Developer Access
```bash
sudo adduser developer
sudo usermod -aG sudo developer
sudo mkdir -p /home/developer/.ssh
sudo touch /home/developer/.ssh/authorized_keys
sudo chmod 700 /home/developer/.ssh
sudo chmod 600 /home/developer/.ssh/authorized_keys
sudo chown -R developer:developer /home/developer/.ssh
echo "SSH_PUBLIC_KEY" | sudo tee -a /home/developer/.ssh/authorized_keys
```

### RDS PostgreSQL Database
```
Engine:     PostgreSQL 18.3
Instance:   db.t3.micro
Storage:    20 GiB gp3
Endpoint:   wonzystore-db.c5o2cscwmnm5.ap-southeast-2.rds.amazonaws.com
Port:       5432
Username:   wonzyadmin
Access:     Private VPC only
Cost:       ~$23.20/month
```

### Application Port
```
Port 8080 opened in security group for Django/Gunicorn
```

### CI/CD Pipeline — GitHub Actions
```
Repo: github.com/Olalekan2040-slack/wonzay
Branch: master
Trigger: push to master
Secrets: EC2_HOST, EC2_USER, EC2_SSH_KEY
```

Deploy key setup:
```bash
ssh-keygen -t ed25519 -C "github-actions-deploy" -f ~/.ssh/github_actions_deploy
cat ~/.ssh/github_actions_deploy.pub >> ~/.ssh/authorized_keys
```

### African IP Geo-blocking
```bash
# Install packages
sudo apt install nginx-extras geoip-database -y
sudo apt install geoipupdate -y
sudo apt install libnginx-mod-http-geoip2 -y

# Configure MaxMind
# Edit /etc/GeoIP.conf with AccountID and LicenseKey
sudo geoipupdate

# Auto-update weekly via cron
0 2 * * 1 geoipupdate && systemctl reload nginx
```

nginx.conf additions:
```nginx
geoip2 /var/lib/GeoIP/GeoLite2-Country.mmdb {
    auto_reload 5m;
    $geoip2_data_country_code country iso_code;
}

map $geoip2_data_country_code $blocked_country {
    default 0;
    NG 1; GH 1; ZA 1; KE 1; EG 1; ET 1; TZ 1;
    UG 1; DZ 1; MA 1; CM 1; CI 1; SN 1; ZW 1;
    ZM 1; MZ 1; AO 1; SD 1; TN 1; LY 1;
}
```

sites-enabled/default blocking rule:
```nginx
set $block 0;
if ($blocked_country) { set $block 1; }
if ($remote_addr = ADMIN_IP) { set $block 0; }
if ($block = 1) { return 403 "Access denied."; }
```

---

## Phase 3 — Pending

- Domain name purchase
- SSL certificate (Let's Encrypt)
- Cloudflare CDN + WAF
- Enhanced geo-blocking via Cloudflare
