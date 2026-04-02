# 🚀 Day 2 – EC2 & First Cloud Server

## 📌 Overview
Today I moved from theory to hands-on by launching and accessing my first virtual server on AWS using EC2.

---

## ⚙️ What is EC2?
Amazon EC2 (Elastic Compute Cloud) is a service that provides scalable virtual servers in the cloud.

- Launch instances on demand  
- Choose OS, CPU, memory, and storage  
- Pay only for what you use  

📌 *It replaces the need for physical servers with flexible cloud infrastructure*

---

## 🌐 Step 1 – VPC Setup
Before launching the instance, I ensured the networking environment was ready:

- Created a VPC with a defined CIDR block  
- Set up a public subnet  
- Attached an Internet Gateway (IGW)  
- Configured Route Tables for internet access  
- Set up a Security Group (firewall rules)  

📌 *This ensures secure and controlled network communication*

---

## 💻 Step 2 – Launching EC2 Instance

- Selected **Amazon Linux** as the operating system  
- Chose a free-tier eligible instance type  
- Placed the instance inside the public subnet  
- Generated a **Key Pair (.pem file)** for secure access  

📌 *Instance successfully launched and running*

---

## 🔑 Step 3 – Secure Access via SSH

To connect to the instance:

- Allowed SSH (port 22) in Security Group (restricted to my IP)  
- Used terminal to connect securely  

### 🔧 Command Used: ssh -i "mykey.pem" ec2-user@<public-ip>


📌 *Successfully accessed the Linux server from my local machine*

---

## 🧠 Key Learnings
- EC2 provides flexible and scalable compute power  
- Networking (VPC) must be configured correctly before deployment  
- Security Groups are critical for controlling access  
- SSH enables secure remote server management  

---

## ✅ Outcome
Successfully launched and connected to a live Linux server on AWS, gaining hands-on experience with cloud compute and networking.

And just like that — I was inside a Linux server running on AWS, controlled from my laptop. 🤯

💡 Day 2 Takeaway:
Theory makes sense on paper.
But the moment you SSH into a cloud server you built yourself?
That's when cloud computing stops being abstract — and becomes real.

Day 3 loading... ⏳

---



