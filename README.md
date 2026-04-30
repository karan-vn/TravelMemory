TravelMemory Deployment (MERN Stack on AWS)


📌 Introduction
TravelMemory is a full-stack MERN application that allows users to store and manage travel experiences. This project demonstrates deployment of a scalable MERN application using AWS infrastructure, reverse proxy configuration, and domain integration.


🎯 Objectives

•	Deploy backend (Node.js + Express) on EC2

•	Deploy frontend (React) on EC2

•	Enable communication between frontend and backend

•	Implement load balancing using AWS ALB

•	Configure reverse proxy using Nginx

•	Connect a custom domain using Cloudflare

•	Ensure scalability and high availability


Tech Stack

🔹 Frontend

•	React — Component-based UI development 

•	Axios — API communication with backend 

•	CSS — Styling and layout 

________________________________________

🔹 Backend

•	Node.js — Server-side execution 

•	Express.js — REST API handling 

•	MongoDB (Atlas) — Cloud database storage 

•	Mongoose — Schema modeling and queries 

________________________________________

🔹 DevOps & Infrastructure

•	Amazon EC2 — Hosting frontend and backend instances 

•	Application Load Balancer — Traffic distribution across backend instances 

•	Nginx — Reverse proxy (backend) + static hosting (frontend) 

•	PM2 — Process management and uptime 

•	Cloudflare — DNS management, CDN, SSL 

________________________________________

🔹 Architecture Highlights 

•	Frontend served via Nginx on EC2 

•	Backend instances behind ALB 

•	API routing through /api endpoints 

•	MongoDB Atlas used as managed database 

•	Horizontal scaling using multiple backend EC2 instances





________________________________________

🚀 Deployment Steps

________________________________________

1.	Creating EC2

Use:

•	AMI: Ubuntu Server 24.04 LTS

•	Instance type: t2.micro (free tier) 

•	Storage: 20GB enough 

•	Security group (IMPORTANT): 

Open:

•	22 → SSH 

•	80 → HTTP 

•	3000 → backend

________________________________________

2.	Installing dependencies

2.1 Update System

sudo apt update && sudo apt upgrade -y

2.2 Install Node.js (v18)

curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -

sudo apt install -y nodejs

2.3 Install Nginx

sudo apt install nginx -y

sudo systemctl start nginx

sudo systemctl enable nginx

2.4 Install Process Manager (PM2)

Sudo npm install -g pm2

________________________________________



3 Clone Repository

git clone https://github.com/UnpredictablePrashant/TravelMemory.git

________________________________________



4. Backend Setup (EC2 Instances)

4.1 cd TravelMemory/backend

npm install

4.2 Configure Environment Variables

Create .env file:

PORT=3000

MONGO_URI=<your_mongodb_connection_string>

4.3 Run Backend

pm2 start index.js

pm2 save

Test:

http://<EC2_PUBLIC_IP>:3000

________________________________________

5. Configure Nginx (Backend Reverse Proxy)

Edit:

sudo nano /etc/nginx/sites-available/default

server {

    listen 80;
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;  
        proxy_cache_bypass $http_upgrade;
    }

}


Restart:

sudo systemctl restart nginx

Test:

http://<EC2_PUBLIC_IP>

________________________________________



6. Frontend Setup (EC2)

6.1 Install & Build

cd TravelMemory/frontend

npm install

npm run build

6.2 Configure API URL

Edit:

src/url.js

export const baseURL = "http://<LOAD_BALANCER_DNS>/api";

6.3 Serve with Nginx

sudo rm -rf /var/www/html/*

sudo cp -r build/* /var/www/html/

sudo systemctl restart nginx

________________________________________



7. Creating additional EC2 for Load Balancer.

7.1 Creating AMI using the configured EC2.

7.2 Creating multiple EC2 using the same AMI.

________________________________________

8. Load Balancer Setup (AWS ALB)

8.1 Create Target Group

•	Type: Instances

•	Protocol: HTTP

•	Port: 80

Add all backend EC2 instances.

Health check:

Path: /

8.2 Create ALB

•	Scheme: Internet-facing

•	Listener: HTTP (port 80)

•	Attach target group

Test:

http://<LOAD_BALANCER_DNS>

________________________________________



9. Scaling (Auto Scaling Group)


•	Create AMI from backend EC2

•	Create Launch Template

•	Create Auto Scaling Group

•	Attach to ALB target group

Configuration:

•	Min: 2

•	Desired: 2

•	Max: 4

________________________________________




10. Domain Setup (Cloudflare)

10.1 Add Domain

•	Add domain to Cloudflare

•	Update nameservers

10.2 DNS Configuration

CNAME:

Name: www

Target: <LOAD_BALANCER_DNS>

A Record:

Name: @

Value: <FRONTEND_EC2_PUBLIC_IP>

Enable proxy (orange cloud).

________________________________________

For pushing the code to a new repository

•	Create a new repository

•	Check the current remote

git remote -v

•	Remove the existing remote 

git remote remove origin

•	Add to new repository

git remote add origin https://github.com/<your-username>/<repo-name>.git

•	Verify 

Git remote -v

•	Push your code

git push -u origin main

It will ask for username and password.

This method is not working anymore.

Check developers tools in your git.

Go to GitHub → Settings

Navigate to:

Developer Settings → Personal access tokens → Tokens (classic)

Click Generate new token (classic)

Select:

✅ repo (full control)

Generate and copy the token (you won’t see it again)

Use this instead of user id and password

________________________________________

________________________________________

✅ Validation Checklist

•	Backend accessible via ALB

•	Frontend loads successfully

•	API calls return valid JSON

•	Load balancing distributes traffic

•	Domain resolves correctly

•	SSL enabled via Cloudflare

________________________________________



Deployment Architecture Diagram
<img width="1302" height="757" alt="Screenshot 2026-04-29 093355" src="https://github.com/user-attachments/assets/497740a6-07db-479a-b46d-540af3474b67" />


________________________________________


Required Screen Shots

1.	Once the Nginx Server is deployed
<img width="940" height="363" alt="image" src="https://github.com/user-attachments/assets/31223f43-21e1-4684-9ad4-f826c753363e" />

2.	Nginx Configuration (Reverse Proxy)
<img width="940" height="385" alt="image" src="https://github.com/user-attachments/assets/ace700eb-3dc5-4b61-b361-bbb63461b26d" />

3.	.env Creation
<img width="940" height="113" alt="image" src="https://github.com/user-attachments/assets/a000232b-c46f-4759-85d4-dfc1b70daeb2" />

4.	Backend PM2 working 
<img width="940" height="192" alt="image" src="https://github.com/user-attachments/assets/942a07c7-9670-44b6-a865-0403251255ca" />

5.	TravelMemory through EC2 IP
<img width="940" height="522" alt="image" src="https://github.com/user-attachments/assets/4f36ba7a-a4c6-4ffd-a882-16d075bc4b1a" />

6.	EC2
<img width="940" height="196" alt="image" src="https://github.com/user-attachments/assets/f9efaed1-f831-4241-874d-599688660d80" />
 
7.	TravelMemory ALB
<img width="940" height="80" alt="image" src="https://github.com/user-attachments/assets/3b565b9d-f709-4bcd-9d27-23893247b6c2" />

8.	TravelMemory through ALB 
<img width="940" height="520" alt="image" src="https://github.com/user-attachments/assets/b6491a00-1ae4-469e-bb97-fd18bd9a9ae0" />

9.	Load Balancer 
<img width="940" height="511" alt="image" src="https://github.com/user-attachments/assets/e2ab548e-6844-4e5d-9ad1-0f8cb06a94e6" />
 
10.	Target Group
<img width="940" height="508" alt="image" src="https://github.com/user-attachments/assets/cd01f911-b990-4bd3-819e-c3a061378660" />
 
11.	Auto Scaling group
<img width="940" height="507" alt="image" src="https://github.com/user-attachments/assets/5dda64f8-1704-474e-b6b8-3c47eae2f6f6" />
 
12.	Backend index.js
<img width="940" height="419" alt="image" src="https://github.com/user-attachments/assets/b99b3259-330c-4e82-91a2-ff38d1818df6" />
 
13.	TravelMemory through custom domain
<img width="940" height="524" alt="image" src="https://github.com/user-attachments/assets/4a5d5f3d-8159-47e8-94dc-759eb275aca3" />
 
14.	TravelMemory Trip description 
<img width="940" height="523" alt="image" src="https://github.com/user-attachments/assets/2b26a9c9-0775-483c-b750-d5731fa3db4d" />
 
15.	Data in MongoDB
<img width="940" height="524" alt="image" src="https://github.com/user-attachments/assets/6198e449-b934-4786-a4b4-2690c341f5ca" />




