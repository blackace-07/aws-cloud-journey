# 🚀 Day 3 — Private Subnet, Bastion Host & NAT Gateway

> AWS Cloud Learning Journey | Lab 2

---

## 🏗️ Architecture
Your Laptop
|
↓ SSH (port 22)
[ Bastion Host ]  ←── Public Subnet  (10.0.1.0/24)
|                      |
↓ SSH hop              ↓ outbound only
[ Private EC2 ] ←── Private Subnet (10.0.2.0/24)
|
[ NAT Gateway ]
|
Internet

---

## 🧠 Concepts Covered

| Concept | What It Does |
|---|---|
| **Bastion Host** | Jump server — only secure entry point to private EC2 |
| **Private Subnet** | No public IP — fully isolated from internet |
| **NAT Gateway** | Lets private EC2 reach internet outbound only |
| **SSH Agent Forwarding** | Carries your key through Bastion securely |
| **Security Group Referencing** | Reference another SG as source instead of IP |

---

## 📋 Steps Covered

### PHASE 1 — VPC
- Created `lab2-vpc` with CIDR `10.0.0.0/16`

### PHASE 2 — Subnets
- `public-subnet` → `10.0.1.0/24` (auto-assign public IP enabled)
- `private-subnet` → `10.0.2.0/24` (no public IP)

### PHASE 3 — Internet Gateway
- Created `lab2-igw` → attached to `lab2-vpc`

### PHASE 4 — Route Tables
- `public-rt` → `0.0.0.0/0` → IGW → `public-subnet`
- `private-rt` → no internet route → `private-subnet`

### PHASE 5 — Security Groups
- `bastion-sg` → SSH from My IP only
- `private-sg` → SSH from `bastion-sg` only

### PHASE 6 — EC2 Instances
- `bastion-host` → public subnet → public IP
- `private-ec2` → private subnet → no public IP

### PHASE 7 — Terminal Commands
```bash
# Enable SSH agent (Windows)
Set-Service ssh-agent -StartupType Manual
Start-Service ssh-agent
ssh-add lab2-key.pem

# SSH into Bastion
ssh -A -i "lab2-key.pem" ec2-user@<bastion-public-ip>

# Hop into Private EC2
ssh ec2-user@<private-ec2-private-ip>

# Test no internet (before NAT)
ping google.com
# Result: 100% packet loss ✅
```

### PHASE 8 — NAT Gateway
- Created `lab2-nat` in `public-subnet` with Elastic IP
- Updated `private-rt` → `0.0.0.0/0` → NAT Gateway
```bash
# Test internet after NAT
ping google.com
# Result: 64 bytes from 142.250.x.x ✅
```

---

## 🔑 Key Learnings

- Bastion Host = YOU get INTO the private server securely
- NAT Gateway = Private server gets OUT to internet safely
- SSH Agent Forwarding (-A flag) = carries key through Bastion
- Private subnet = no public IP + no IGW route = truly isolated
- Security Groups can reference each other as source

---

## 📝 Terminal Cheat Sheet
```bash
pwd                           # where am I?
ls                            # list files
cd Downloads                  # navigate to folder
chmod 400 lab2-key.pem        # fix key permissions (Mac)
ssh-add lab2-key.pem          # add key to SSH agent
ssh -A -i "key.pem" user@IP   # SSH with agent forwarding
ssh user@privateIP            # hop to private server
ping google.com               # test internet
Ctrl+C                        # stop running command
exit                          # disconnect from server
```

---

## 🧹 Cleanup Order

1. ✅ Terminate both EC2 instances
2. ✅ Delete NAT Gateway
3. ✅ Wait until NAT is fully deleted
4. ✅ Release Elastic IP
5. ✅ Delete VPC

> ⚠️ NAT Gateway charges by the hour — always delete after lab!

---

## 🔗 Connect With Me

- 💼 [LinkedIn](https://www.linkedin.com/in/mdviquar)
- 📧 mohdviquar123@gmail.com