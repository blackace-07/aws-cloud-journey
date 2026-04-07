# 🚀 AWS Day 5 — VPC Peering Lab (Production-Level Guide)

---

## 📌 Objective

Design and implement **secure, private communication between two isolated VPCs** using VPC Peering, simulating a real-world **Application Tier ↔ Data Tier architecture**.

---

## 🧠 Core Concepts (Interview-Ready)

- **VPC Peering** = Private connection between two VPCs using AWS backbone
- No Internet Gateway, NAT Gateway, or VPN required
- **CIDR blocks must not overlap**
- **Non-Transitive** → Peering is strictly one-to-one
- **Manual Route Configuration required on both sides**
- **DNS resolution is disabled by default across VPCs**
- Communication happens via **private IP only**

---

## 🏗️ Architecture Overview

    VPC A (App Tier)                  VPC B (Data Tier)
    10.0.0.0/16                      172.16.0.0/16
 ┌─────────────────┐              ┌─────────────────┐
 │  EC2 (Web/API)  │              │  EC2 (DB/Test)  │
 │  10.0.1.0/24    │              │  172.16.1.0/24  │
 └────────┬────────┘              └────────┬────────┘
          │                                │
          └──────── VPC Peering ───────────┘
              (Private AWS Network)

              ---

## ⚙️ Lab Implementation (Step-by-Step)

---

### 🔹 Step 1 — Create VPC A (Application Layer)

- Name: `VPC-App`
- CIDR Block: `10.0.0.0/16`
- Enable:
  - DNS Resolution ✅
  - DNS Hostnames ✅

#### Subnet:
- Name: `App-Subnet`
- CIDR: `10.0.1.0/24`

---

### 🔹 Step 2 — Create VPC B (Data Layer)

- Name: `VPC-Data`
- CIDR Block: `172.16.0.0/16`
- Enable:
  - DNS Resolution ✅
  - DNS Hostnames ✅

#### Subnet:
- Name: `Data-Subnet`
- CIDR: `172.16.1.0/24`

---

### 🔹 Step 3 — Launch EC2 Instances

#### EC2 in VPC A (App Server)
- AMI: Amazon Linux 2
- Instance Type: t2.micro
- Subnet: `10.0.1.0/24`
- Security Group:
  - Allow ICMP (Ping)
  - Allow SSH (optional)

#### EC2 in VPC B (Data Server)
- AMI: Amazon Linux 2
- Instance Type: t2.micro
- Subnet: `172.16.1.0/24`
- Security Group:
  - Allow ICMP
  - Allow SSH

---

### 🔹 Step 4 — Create VPC Peering Connection

1. Navigate to **VPC Dashboard → Peering Connections**
2. Click **Create Peering Connection**

#### Configuration:
- Name: `App-to-Data-Peering`
- Requester VPC: `VPC-App`
- Accepter VPC: `VPC-Data`

3. Create request  
4. Select → **Accept Request**

---

### 🔹 Step 5 — Update Route Tables (Critical Step)

#### 📍 VPC A Route Table

Add route:

Destination: 172.16.0.0/16
Target: VPC Peering Connection


#### 📍 VPC B Route Table

Add route:

Destination: 10.0.0.0/16
Target: VPC Peering Connection

⚠️ **Important:**  
Routing must be configured on **both sides** or traffic will fail (asymmetric routing issue)

---

### 🔹 Step 6 — Configure Security Groups

#### VPC A Security Group:
- Allow Inbound:
  - Type: All Traffic
  - Source: `172.16.0.0/16`

#### VPC B Security Group:
- Allow Inbound:
  - Type: All Traffic
  - Source: `10.0.0.0/16`

---

### 🔹 Step 7 — Enable DNS Resolution Across Peering

1. Go to **Peering Connection**
2. Actions → **Edit DNS Settings**

Enable:
- DNS resolution from requester ✅
- DNS resolution from accepter ✅

---

### 🔹 Step 8 — Validate Connectivity

SSH into EC2 in VPC A:

```bash
ping <Private-IP-of-EC2-in-VPC-B>

Expected Result:

PING successful ✅

Optional:

curl <private-ip>