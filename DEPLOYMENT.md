# Deployment Log — Phase 1

Full step-by-step record of every action taken to deploy the Wonzystore e-commerce infrastructure on AWS.

**Date:** June 13, 2026
**Engineer:** Mozypelly
**AWS Account:** Mozytech (522196680583)
**Region:** Asia Pacific (Sydney) — ap-southeast-2

---

## Step 1 — AWS Account Creation

- Created new AWS account at aws.amazon.com
- Used dedicated business email: wonzstore.admin@gmail.com
- Account name: Mozytech
- Selected Basic Support plan (free)
- Card verified with $1 temporary charge (auto-refunded)

---

## Step 2 — Account Security

### Root account MFA
```
IAM Console > Security Credentials > Assign MFA Device
Type: Authenticator app (Google Authenticator)
Scanned QR code > entered two consecutive 6-digit codes
Result: MFA active on root account
```

### IAM user creation
```
IAM Console > Users > Create User
Username: wonzystore-admin
Console access: enabled
Password: custom (saved securely)
Policy: AdministratorAccess
MFA: enabled (Google Authenticator)
```

### Account alias
```
IAM Dashboard > Account Alias > Create
Alias: wonzystore
Login URL: https://wonzystore.signin.aws.amazon.com/console
```

---

## Step 3 — Billing Protection

### Zero-spend budget
```
Billing > Budgets > Create Budget
Template: Zero spend budget
Name: AWS-Zero-Spend-Alert
Email: wonzstore.admin@gmail.com
Threshold: $0.01
```

### Cost Explorer activated
```
Billing > Cost Explorer > Enable
Status: Active (data populates within 24 hours)
```

---

## Step 4 — EC2 Instance Launch

### Configuration
```
EC2 Console > Launch Instance
Name:          wonzystore-webserver
AMI:           Ubuntu Server 24.04 LTS (ami-020728ad6199d7fa0)
Instance type: t2.micro (free tier)
Storage:       20 GiB gp3
Key pair:      wonzystore-keypair (RSA, .pem)
```

### Key pair storage on Mac
```bash
mkdir -p ~/.ssh
mv ~/Downloads/wonzystore-keypair.pem ~/.ssh/
chmod 400 ~/.ssh/wonzystore-keypair.pem
```

### Security group — wonzystore-sg
```
Rule 1: SSH    TCP  22   Source: Admin IP only (105.127.13.11/32)
Rule 2: HTTP   TCP  80   Source: 0.0.0.0/0
Rule 3: HTTPS  TCP  443  Source: 0.0.0.0/0
```

### Instance details
```
Instance ID:  i-0f4706a0d42cb33f7
Public IP:    3.107.29.242
Private IP:   172.31.0.72
State:        Running
```

---

## Step 5 — Server Configuration

### SSH connection
```bash
ssh -i ~/.ssh/wonzystore-keypair.pem ubuntu@3.107.29.242
```

### System update
```bash
sudo apt update && sudo apt upgrade -y
```

### Nginx installation
```bash
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl status nginx
# Result: active (running)
```

### UFW firewall
```bash
sudo apt install ufw -y
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
sudo ufw status
```

### UFW status output
```
Status: active
To                  Action    From
--                  ------    ----
OpenSSH             ALLOW     Anywhere
Nginx Full          ALLOW     Anywhere
OpenSSH (v6)        ALLOW     Anywhere (v6)
Nginx Full (v6)     ALLOW     Anywhere (v6)
```

### Verification
- Opened http://3.107.29.242 in browser
- Result: Nginx welcome page confirmed
- Server is live and publicly accessible

---

## Phase 2 — Pending

Waiting on developer to confirm tech stack, then proceeding with:
- RDS database provisioning
- Backend stack installation on EC2
- Application code deployment
- Domain and SSL setup
- AWS WAF with African IP geo-blocking
- S3 and CloudFront configuration
