# 🛒 Wonzystore — AWS E-Commerce Deployment

![AWS](https://img.shields.io/badge/Cloud-AWS-FF9900?style=flat&logo=amazonaws&logoColor=white)
![Ubuntu](https://img.shields.io/badge/OS-Ubuntu%2024.04%20LTS-E95420?style=flat&logo=ubuntu&logoColor=white)
![Nginx](https://img.shields.io/badge/Web%20Server-Nginx-009639?style=flat&logo=nginx&logoColor=white)
![Status](https://img.shields.io/badge/Phase%201-Complete-brightgreen?style=flat)
![Region](https://img.shields.io/badge/Region-ap--southeast--2%20Sydney-blue?style=flat)

A full production-grade AWS cloud infrastructure deployment for an e-commerce web platform targeting the Australian market. Built and managed by a Network Security Engineer with 17+ years of experience.

---

## 🏗 Architecture Overview

![AWS Architecture Diagram](./architecture.svg)

---

## ✅ Phase 1 — Completed

### AWS Account Setup
- Created dedicated AWS account with business email
- Selected Asia Pacific (Sydney) `ap-southeast-2` as deployment region
- Configured Basic Support plan (free tier)
- Enabled Cost Explorer and Zero-Spend billing alert

### IAM & Identity Security
- Root account secured and isolated from daily operations
- IAM admin user created: `wonzystore-admin`
- MFA (Google Authenticator) enabled on both root and IAM user
- `AdministratorAccess` policy attached to IAM user
- Custom account alias configured: `wonzystore`

### EC2 Server Provisioning
| Property | Value |
|---|---|
| Instance ID | `i-0f4706a0d42cb33f7` |
| Instance Type | `t2.micro` (1 vCPU, 1 GiB RAM) |
| OS | Ubuntu Server 24.04 LTS |
| AMI | `ami-020728ad6199d7fa0` |
| Public IP | `3.107.29.242` |
| Storage | 20 GiB gp3 (3000 IOPS) |
| Region | ap-southeast-2 (Sydney) |

### Security Group — `wonzystore-sg`
| Rule | Protocol | Port | Source |
|---|---|---|---|
| SSH | TCP | 22 | Admin IP only |
| HTTP | TCP | 80 | 0.0.0.0/0 |
| HTTPS | TCP | 443 | 0.0.0.0/0 |

### Web Server & Firewall
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install nginx -y
sudo systemctl enable nginx
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

---

## ⏳ Phase 2 — Pending

- [ ] RDS Database (MySQL / PostgreSQL / MongoDB)
- [ ] Backend application stack installation
- [ ] Application code deployment
- [ ] Domain configuration via Route 53
- [ ] SSL/HTTPS certificate (Let's Encrypt)
- [ ] AWS WAF with African IP geo-blocking
- [ ] S3 bucket for product images
- [ ] CloudFront CDN configuration

---

## 💰 Estimated Monthly Cost

| Service | Cost |
|---|---|
| EC2 t2.micro (free tier) | $0.00 |
| RDS db.t3.micro | ~$15–20 |
| S3 storage | ~$1–5 |
| Route 53 | ~$0.50 |
| SSL Certificate | Free |
| AWS WAF | ~$5–10 |
| **Total** | **~$22–36/month** |

---

## 🔒 Security Design

| Layer | Control |
|---|---|
| Application | AWS WAF (OWASP rules + geo-block) |
| Network | AWS Security Group (least privilege) |
| Host | UFW firewall on EC2 |
| Identity | IAM + MFA + root isolation |
| Access | SSH key-based auth, no password login |

---

## 👨‍💻 Author

**Mozypelly** — Network Security Engineer & Ethical Hacker
- 17+ years in network engineering and cybersecurity
- 𝕏: [@mozypellyXP](https://x.com/mozypellyXP)
- GitHub: [Mozypelly10](https://github.com/Mozypelly10)
