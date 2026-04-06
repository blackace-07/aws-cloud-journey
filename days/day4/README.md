# 🚀 Day 4 — Private Subnet, NAT Gateway & Linux/Shell Commands

> AWS Cloud Learning Journey | Theory Day

---

## 🏗️ What I Covered Today

- Deep dive into Private Subnets
- NAT Gateway concept & how it works
- Linux & Shell basics
- SSH commands for cloud access

---

## 🔒 Private Subnet — What "Private" Actually Means

A private subnet isn't just a subnet without a public IP.
It's a deliberate architectural decision.

When you create a subnet with **no route to an Internet Gateway**:
→ Nothing from the internet can reach it
→ Nothing inside can reach the internet either
→ Resources exist only within the VPC

That's where databases, internal APIs, and sensitive workloads live.

> 💡 Private ≠ hidden by accident. Private = **protected by design.**

### Key Points
- No IGW route = no internet = truly private
- Used for databases, app servers, internal services
- Only reachable from within the VPC or via Bastion/NAT

---

## 🌐 NAT Gateway — The One-Way Door

NAT Gateway is a **translation tool** that enforces direction.

It converts your private EC2's internal IP into a public-facing one
to go out — but there's **no reverse path**.

→ Private EC2 needs an update? NAT lets it out ✅
→ Internet tries to reach private EC2? Dead end ❌

### How It Works
Private EC2 (10.0.2.x)
|
↓ outbound request
NAT Gateway (public subnet)
|
↓ translates private IP → public IP
Internet
|
↓ response comes back to NAT
NAT Gateway
|
↓ forwards back to Private EC2
Private EC2 ✅

### Key Points
- Must sit in **public subnet**
- Needs an **Elastic IP**
- Outbound only — internet cannot initiate inbound
- Update **private route table** → `0.0.0.0/0` → NAT Gateway

---

## 💻 Linux & Shell — The Basics

### Navigation Commands
```bash
pwd                    # where am I right now?
ls                     # list files in current folder
ls -la                 # list all files including hidden ones
cd foldername          # go into a folder
cd ..                  # go back one level
cd ~                   # go to home directory
cd /                   # go to root directory
clear                  # clear the terminal screen
```

### File & Folder Commands
```bash
mkdir foldername       # create a new folder
touch filename.txt     # create a new empty file
cat filename.txt       # read/display a file
nano filename.txt      # open file to edit (simple editor)
cp file1 file2         # copy a file
mv file1 file2         # move or rename a file
rm filename            # delete a file
rm -rf foldername      # delete a folder and everything in it
```

### System Commands
```bash
whoami                 # shows your current username
hostname               # shows the machine name
uname -a               # shows OS and system info
df -h                  # shows disk space usage
free -h                # shows memory usage
top                    # live process monitor (like Task Manager)
history                # shows all commands you've typed
```

### Network Commands
```bash
ping google.com        # test internet connectivity
curl https://google.com  # fetch a webpage
ifconfig               # show network interfaces (older)
ip addr                # show IP addresses (modern)
netstat -tulnp         # show open ports
```

### Permissions Commands
```bash
chmod 400 key.pem      # make file read-only (for SSH keys)
chmod 755 script.sh    # make script executable
chmod 644 file.txt     # standard file permission
ls -l                  # see permissions of all files
```

### Package Management (Amazon Linux)
```bash
sudo yum update        # update all packages
sudo yum install wget  # install a package
sudo yum remove wget   # remove a package
```

---

## 🔑 SSH Commands — Cloud Access
```bash
# Fix key permissions before using (Mac/Linux)
chmod 400 lab2-key.pem

# Add key to SSH agent (Windows PowerShell)
Set-Service ssh-agent -StartupType Manual
Start-Service ssh-agent
ssh-add lab2-key.pem

# Basic SSH connection
ssh -i "key.pem" ec2-user@<public-ip>

# SSH with Agent Forwarding (for Bastion → Private EC2)
ssh -A -i "key.pem" ec2-user@<bastion-public-ip>

# Hop from Bastion to Private EC2
ssh ec2-user@<private-ec2-private-ip>

# Test connectivity
ping google.com

# Stop any running command
Ctrl+C

# Disconnect from server
exit
```

### What -A Flag Does
The `-A` flag enables **SSH Agent Forwarding**
→ Your key travels with you through the Bastion
→ No need to copy the key file onto the server
→ Clean, secure, professional ✅

---

## 🧠 Key Takeaways

| Concept | One Line Summary |
|---|---|
| Private Subnet | No IGW route = no internet = protected by design |
| NAT Gateway | Private EC2 goes OUT — internet cannot come IN |
| SSH -A flag | Agent Forwarding carries your key securely |
| chmod 400 | Locks down key file so only you can read it |
| ping | Quick way to test if internet is working |

---

## 💡 Day 4 Reflection

Theory without a lab still builds something — it builds the **why** behind every click.

When the lab comes tomorrow, I won't just be following steps.
I'll know exactly what each component is doing and why it's placed where it is.

That's the difference between **doing** cloud and **understanding** cloud.

---

## 🔗 Connect With Me

- 💼 [LinkedIn](https://www.linkedin.com/in/mdviquar)
- 📧 mohdviquar123@gmail.com