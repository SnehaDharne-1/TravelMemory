# TravelMemory Deployment on AWS EC2 using MERN Stack

## Project Overview

<img width="1279" height="301" alt="Travelmemory" src="https://github.com/user-attachments/assets/785ed40a-337d-4fcd-9f17-5192ef43c02b" />

# AWS System Architecture

This project demonstrates the deployment of the **TravelMemory** MERN Stack application on **Amazon EC2** with:

- Backend deployment using Node.js
- Frontend deployment using React
- Nginx reverse proxy configuration
- Load balancing with multiple instances
- Custom domain integration using Cloudflare
- Scalable and production-ready architecture

---

# Project Repository

Repository Link:  
👉 <https://github.com/UnpredictablePrashant/TravelMemory>

---

# Deployment Architecture

## Architecture Components

- **Frontend:** React Application
- **Backend:** Node.js + Express
- **Database:** MongoDB
- **Web Server:** Nginx
- **Cloud Platform:** AWS EC2
- **Load Balancer:** AWS Application Load Balancer (ALB)
- **DNS & Security:** Cloudflare

---

# Deployment Architecture Diagram

> Create the diagram using [draw.io](https://www.draw.io/)

## Suggested Architecture Flow

```text
User
  │
  ▼
Cloudflare DNS
  │
  ▼
AWS Load Balancer
  │
  ├──────────────┐
  ▼              ▼
Frontend EC2   Frontend EC2
  │              │
  ▼              ▼
Backend EC2    Backend EC2
  │              │
  └──────► MongoDB
```

---

# Prerequisites

Before starting, ensure you have:

- AWS Account
- EC2 Instance (Ubuntu 22.04 Recommended)
- Domain Name
- Cloudflare Account
- Node.js installed
- MongoDB Atlas or MongoDB Server
- Git installed

---

# Step 1: Launch EC2 Instance

## Create EC2 Instance

1. Login to AWS Console
2. Navigate to EC2 Dashboard
3. Launch Instance
4. Choose:
   - Ubuntu Server 22.04 LTS
   - t2.micro (Free Tier)
5. Configure Security Group:
   - SSH (22)
   - HTTP (80)
   - HTTPS (443)
   - Custom TCP (3000)

---

# Step 2: Connect to EC2

```bash
ssh -i your-key.pem ubuntu@your-ec2-public-ip
```

---

# Step 3: Install Required Packages

## Update System

```bash
sudo apt update && sudo apt upgrade -y
```

## Install Git

```bash
sudo apt install git -y
```

## Install Node.js

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
```

## Install Nginx

```bash
sudo apt install nginx -y
```

## Install PM2

```bash
sudo npm install -g pm2
```

---

# Step 4: Clone Repository

```bash
git clone https://github.com/UnpredictablePrashant/TravelMemory.git
cd TravelMemory
```

---

# Step 5: Backend Configuration

## Navigate to Backend

```bash
cd backend
```

## Install Dependencies

```bash
npm install
```

## Configure Environment Variables

Create `.env` file:

```env
PORT=3000
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_secret_key
```

## Start Backend Server

```bash
pm2 start index.js --name backend
```

## Verify PM2 Process

```bash
pm2 status
```

---

# Step 6: Configure Nginx Reverse Proxy

## Create Nginx Configuration

```bash
sudo nano /etc/nginx/sites-available/travelmemory
```

## Add Configuration

```nginx
server {
    listen 80;

    server_name yourdomain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;

        proxy_cache_bypass $http_upgrade;
    }
}
```

## Enable Configuration

```bash
sudo ln -s /etc/nginx/sites-available/travelmemory /etc/nginx/sites-enabled/
```

## Test Nginx

```bash
sudo nginx -t
```

## Restart Nginx

```bash
sudo systemctl restart nginx
```

---

# Step 7: Frontend Configuration

## Navigate to Frontend

```bash
cd ../frontend
```

## Install Dependencies

```bash
npm install
```

---

# Step 8: Update Frontend API URL

Navigate to:

```text
frontend/src/urls.js
```

Update backend URL:

```javascript
const baseURL = "http://yourdomain.com";
```

---

# Step 9: Build Frontend

```bash
npm run build
```

---

# Step 10: Serve Frontend using Nginx

## Configure Nginx for Frontend

```bash
sudo nano /etc/nginx/sites-available/frontend
```

## Add Configuration

```nginx
server {
    listen 80;
    server_name yourdomain.com;

    root /home/ubuntu/TravelMemory/frontend/build;
    index index.html;

    location / {
        try_files $uri /index.html;
    }
}
```

## Enable Frontend Configuration

```bash
sudo ln -s /etc/nginx/sites-available/frontend /etc/nginx/sites-enabled/
```

## Restart Nginx

```bash
sudo systemctl restart nginx
```

---

# Step 11: Scaling the Application

## Create Multiple Backend Instances

Clone backend on multiple EC2 instances or run multiple PM2 processes:

```bash
pm2 start index.js -i max
```

---

# Step 12: Configure AWS Load Balancer

## Create Target Groups

1. Go to EC2 Dashboard
2. Select Target Groups
3. Create:
   - Frontend Target Group
   - Backend Target Group

## Create Application Load Balancer

1. Go to Load Balancers
2. Create Application Load Balancer
3. Add Listener:
   - HTTP : 80
4. Attach Target Groups

---

# Step 13: Configure Cloudflare Domain

## Add Domain to Cloudflare

1. Login to Cloudflare
2. Add your domain
3. Change nameservers from domain provider

---

# Step 14: Create DNS Records

## Create CNAME Record

| Type | Name | Target |
|------|------|------|
| CNAME | www | load-balancer-dns |

## Create A Record

| Type | Name | IPv4 Address |
|------|------|------|
| A | @ | EC2 Public IP |

---

# Step 15: Enable HTTPS (Recommended)

## Install Certbot

```bash
sudo apt install certbot python3-certbot-nginx -y
```

## Generate SSL Certificate

```bash
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

---

# Step 16: Verify Deployment

Check:

- Frontend accessible
- Backend API working
- Load Balancer distributing traffic
- Domain resolving properly
- HTTPS enabled

---

# Useful Commands

## PM2 Commands

```bash
pm2 status
pm2 logs
pm2 restart backend
pm2 stop backend
```

## Nginx Commands

```bash
sudo systemctl status nginx
sudo systemctl restart nginx
sudo nginx -t
```

---

# Security Best Practices

- Enable HTTPS using SSL
- Restrict unnecessary ports
- Use environment variables for secrets
- Enable Cloudflare protection
- Regularly update packages
- Use PM2 for process management

---

# Screenshots to Include

## Suggested Screenshots

- EC2 Instance Running
- Security Group Configuration
- PM2 Running Processes
- Nginx Configuration
- Application Running in Browser
- AWS Load Balancer
- Cloudflare DNS Settings
- HTTPS Enabled Website

---

# Final Deliverables

✅ Fully deployed TravelMemory application  
✅ Backend running on Node.js  
✅ React frontend connected successfully  
✅ Nginx reverse proxy configured  
✅ Multiple instances with Load Balancer  
✅ Custom domain connected via Cloudflare  
✅ SSL/HTTPS enabled  
✅ Complete documentation with screenshots  
✅ Deployment architecture diagram  

---

# Challenges Faced

- Configuring reverse proxy using Nginx
- Connecting frontend and backend properly
- Managing multiple server instances
- DNS propagation delays in Cloudflare
- SSL certificate configuration

---

# Conclusion

Successfully deployed the TravelMemory MERN Stack application on AWS EC2 with:

- Production-ready setup
- Scalable architecture
- Load balancing
- Cloudflare integration
- Secure HTTPS communication

This deployment demonstrates practical knowledge of cloud deployment, DevOps practices, scalability, and full-stack hosting.

---

# Author

**Your Name**

GitHub: <https://github.com/your-github-username>

---

`.env` file to work with the backend after creating a database in mongodb: 

```
MONGO_URI='ENTER_YOUR_URL'
PORT=3001
```

Data format to be added: 

```json
{
    "tripName": "Incredible India",
    "startDateOfJourney": "19-03-2022",
    "endDateOfJourney": "27-03-2022",
    "nameOfHotels":"Hotel Namaste, Backpackers Club",
    "placesVisited":"Delhi, Kolkata, Chennai, Mumbai",
    "totalCost": 800000,
    "tripType": "leisure",
    "experience": "Lorem Ipsum, Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum, ",
    "image": "https://t3.ftcdn.net/jpg/03/04/85/26/360_F_304852693_nSOn9KvUgafgvZ6wM0CNaULYUa7xXBkA.jpg",
    "shortDescription":"India is a wonderful country with rich culture and good people.",
    "featured": true
}
```


For frontend, you need to create `.env` file and put the following content (remember to change it based on your requirements):
```bash
REACT_APP_BACKEND_URL=http://localhost:3001
```
