# Day 6 — Inter-Region VPC Peering (Mumbai ↔ N. Virginia)

## 📌 What is VPC Peering?
VPC Peering is a networking connection between two VPCs that 
enables routing traffic between them using private IP addresses.
Inter-region peering works across different AWS regions over 
AWS's private backbone network — traffic never touches the 
public internet.

---

## 🏗️ Architecture
Your Laptop (MobaXterm)
|
|--SSH--> Bastion-Mumbai (Public IP)
|                |
|                |--SSH--> Private-Mumbai (10.0.2.x)
|
|--SSH--> Bastion-Virginia (Public IP)
|
|--SSH--> Private-Virginia (172.31.2.x)
Mumbai VPC (10.0.0.0/16) <===VPC Peering===> Virginia VPC (172.31.0.0/16)

---

## 📋 Resources Created

| Resource | Mumbai (ap-south-1) | Virginia (us-east-1) |
|---|---|---|
| VPC | 10.0.0.0/16 | 172.31.0.0/16 |
| Public Subnet | 10.0.1.0/24 | 172.31.1.0/24 |
| Private Subnet | 10.0.2.0/24 | 172.31.2.0/24 |
| Internet Gateway | IGW-Mumbai | IGW-Virginia |
| Bastion Host | Public IP — Amazon Linux | Public IP — Amazon Linux |
| Private EC2 | No public IP | No public IP |
| Peering Route | 172.31.0.0/16 → pcx | 10.0.0.0/16 → pcx |

---

## 🔧 Step by Step — What I Did

### Phase 1 — VPCs
- Created VPC-Mumbai-Lab (10.0.0.0/16) in ap-south-1
- Created VPC-Virginia-Lab (172.31.0.0/16) in us-east-1
- Enabled DNS hostnames and DNS resolution on both VPCs

### Phase 2 — Subnets, IGW, Route Tables
- Created public and private subnets in each region
- Enabled auto-assign public IP on public subnets only
- Created and attached Internet Gateways to both VPCs
- Created public route tables with 0.0.0.0/0 → IGW
- Created private route tables with no internet route

### Phase 3 — Security Groups
- SG-Bastion: SSH port 22 from my IP only
- SG-Private: SSH from bastion SG + nc ports 
  (1234, 4444, 5555) + ICMP from peer VPC CIDR
- Critical lesson: SG must be created in correct VPC

### Phase 4 — EC2 Instances
- Launched 4 instances total:
  - Bastion-Mumbai → Public-Subnet-Mumbai
  - Private-Mumbai → Private-Subnet-Mumbai (no public IP)
  - Bastion-Virginia → Public-Subnet-Virginia
  - Private-Virginia → Private-Subnet-Virginia (no public IP)
- Created separate key pairs per region

### Phase 5 — VPC Peering Connection
- Created peering request FROM Mumbai TO Virginia
- Switched to Virginia and ACCEPTED the request
- Status changed to Active ✅

### Phase 6 — Route Tables Updated
- Mumbai public + private RT: added 172.31.0.0/16 → pcx
- Virginia public + private RT: added 10.0.0.0/16 → pcx

### Phase 7 — MobaXterm Setup
- Configured SSH sessions for both bastions
- Used .pem key files for authentication
- Copied .pem keys to bastions via drag and drop
- SSH hopped from bastion → private EC2 in both regions

### Phase 8 — Advanced nc Testing
- Installed nmap-ncat on all instances
- Ran 9 advanced tests across the peering link

---

## 🧪 nc (Netcat) Tests

### Test 1 — Ping Across Peering
```bash
# From Mumbai Private EC2
ping 172.31.2.x -c 5
# Result: ~180ms latency (Mumbai to Virginia)
```

### Test 2 — TCP Port Connectivity
```bash
# Virginia Private EC2 (listener)
ncat -lvp 1234

# Mumbai Private EC2 (sender)
ncat 172.31.2.x 1234
# Result: bidirectional TCP working ✅
```

### Test 3 — File Transfer Mumbai → Virginia
```bash
# Create file on Mumbai
echo 'File from Mumbai over VPC Peering' > /tmp/test_file.txt

# Virginia (receiver)
ncat -lvp 4444 > /tmp/received_file.txt

# Mumbai (sender)
ncat -w 3 172.31.2.x 4444 < /tmp/test_file.txt

# Verify on Virginia
cat /tmp/received_file.txt
```

### Test 4 — File Transfer Virginia → Mumbai
```bash
# Mumbai (receiver)
ncat -lvp 4444 > /tmp/from_virginia.txt

# Virginia (sender)
ncat -w 3 10.0.2.x 4444 < /tmp/va_file.txt
```

### Test 5 — Live TCP Chat Between Regions
```bash
# Virginia (listener)
ncat -lvp 5555

# Mumbai (connect)
ncat 172.31.2.x 5555
# Result: real-time chat across regions ✅
```

### Test 6 — Port Scan Across Peering
```bash
# From Mumbai
ncat -zv 172.31.2.x 22 1234 4444 5555 2>&1
```

### Test 7 — Bandwidth Test
```bash
# Virginia (receiver)
ncat -lvp 5555 > /dev/null

# Mumbai (send 100MB)
dd if=/dev/zero bs=1M count=100 | ncat -w 5 172.31.2.x 5555
```

### Test 8 — TCP Latency Measurement
```bash
# Virginia (listener)
ncat -lvp 1234

# Mumbai (measure time)
time ncat -zv 172.31.2.x 1234
# Result: ~180ms real TCP handshake time
```

### Test 9 — Multi-hop nc Relay
```bash
# Virginia Private (final destination)
ncat -lvp 7777

# Virginia Bastion (relay)
mkfifo /tmp/relay_pipe
ncat -lvp 6666 < /tmp/relay_pipe | ncat 172.31.2.x 7777 > /tmp/relay_pipe

# Mumbai Private (connect to relay)
ncat 172.31.1.x 6666
```

---

## 💡 Key Concepts Learned

**VPC Peering Rules:**
- Peering is NOT transitive
- CIDRs must not overlap
- Routes must be added in BOTH directions
- Works across regions over AWS private backbone

**Security Group Rules:**
- Always attach SG to correct VPC
- Reference SGs by ID not name
- Private EC2 only allows traffic from bastion SG

**Bastion Host Pattern:**
- Only entry point to private subnet
- Has public IP in public subnet
- Private EC2 has zero public exposure

---

## ⚠️ Challenges Faced & Fixes

| Challenge | Fix |
|---|---|
| SG created in wrong VPC | Recreate SG and select correct VPC |
| nc not found | sudo dnf install nmap-ncat -y |
| Network timeout SSH | Update SG with current public IP |
| Peering route not showing | Accept peering first then add route |
| Private EC2 no internet | Install via bastion then SCP binary |
| mkdir day 6 error (PowerShell) | Use mkdir day-6 (no spaces) |

---

## 📸 Screenshots

### VPC Peering Connection — Active Status
![VPC Peering Active](screenshots/VPC%20Peering%20Connection%20showing%20Active%20status.png)

### Route Table Mumbai — Peering Route
![Route Table Mumbai](screenshots/Route%20table%20in%20Mumbai%20showing%20→%20pcx-xxxxx.png)

### Route Table Virginia — Peering Route
![Route Table Virginia](screenshots/Route%20table%20in%20Virginia%20showing%20→%20pcx-xxxxx.png)

### Ping Output Across Peering (Test 1)
![Ping Test](screenshots/Ping%20output%20across%20peering%20(Test%201).png)

### EC2 Instances — Mumbai
![EC2 Mumbai](screenshots/EC2%20Instances%20list%20Mumbai.png)

### EC2 Instances — Virginia
![EC2 Virginia](screenshots/EC2%20Instances%20Virginia%20.png)

---

## 🛠️ Tools Used
- AWS Management Console
- MobaXterm (Windows SSH client)
- ncat/netcat (network testing)
- VS Code + Git (documentation)

---

## 🔗 Connect
Follow my AWS learning journey — Day 7 coming soon!

#AWS #Networking #VPC #CloudComputing #DevOps 