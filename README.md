# Travel Memory Application Deployment
The Travel Memory application has been developed using the MERN stack. This project aims to deploy a production-ready MERN application on AWS, using separate EC2 instances for frontend and backend, NGINX as a reverse proxy, Cloudflare for secure public access, and an AWS Application Load Balancer to handle failures and distribute traffic.

## Project Objectives and Scope
1. Deploy a MERN application on AWS EC2 instances.
2. Centralize traffic through NGINX, routing frontend and backend paths appropriately.
3. Use an AWS Application Load Balancer (ALB) so that backend failures on one instance do not cause downtime.
4. Securely expose the application over HTTPS using Cloudflare.

## Tech Stack
1. Frontend
    1. React.js
    2. CSS
2. Backend
    1. Node.js
    2. MongoDB (Atlas)
3. Infrastructure
    1. AWS EC2 (Compute)
    2. Nginx (Reverse Proxy + Static Hosting)
    3. PM2 (Process Manager)
    4. AWS Application Load Balancer (ALB)
    5. Cloudflare (DNS, CDN, SSL)

## Task 1: Backend Configuration: Clone the repository and navigate to the backend directory
#### Step 1: Launch EC2 Instance
1. Go to AWS Console → EC2
2. Click Launch Instance
3. Configure:
    1. AMI: Ubuntu 
    2. Instance type: t2.micro (free tier)
    3. Key pair: create/download
    4. Security Group:
         1. SSH → 22 (Anywhere)
         2. HTTP → 80 (Anywhere) <img width="3002" height="344" alt="image" src="https://github.com/user-attachments/assets/18514d88-233e-4f87-a8bb-1ed775cc41a4" />
            <img width="3438" height="66" alt="image" src="https://github.com/user-attachments/assets/f0e7a28b-b6ef-4e67-8e26-66826fd537bd" />

#### Step 2: Connect to EC2
1. `ssh -i PEM_KEY.pem ubuntu@65.0.7.136`
   <img width="2402" height="106" alt="image" src="https://github.com/user-attachments/assets/28cf3bdb-979b-4f1e-9b25-e2b05d8ffdbf" />
#### Step 3: Install Required Software
1. Update app - `sudo apt update && sudo apt upgrade -y`
2. Install Node.js (LTS) - 
`curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`
`sudo apt install -y nodejs`
3. Install Git - 
`sudo apt install git -y`
4. Install Nginx - 
`sudo apt install nginx -y`
5. Install PM2 (process manager) - 
`sudo npm install -g pm2`
#### Step 4: Clone Repository
1. Cloning - `git clone https://github.com/suhailm67-sys/Travel_Memory_App.git`
2. Create directory - `cd Travel_Memory_App/backend`
#### Step 5: Install Backend Dependencies
1. `npm install`
   <img width="1332" height="310" alt="image" src="https://github.com/user-attachments/assets/c15b52d0-d511-470c-a367-1d504849b32f" />
#### Step 6: Configure Environment Variables
Use MongoDB Atlas instead of local MongoDB. Go to MongoDB Atlas >> Create cluster >> Whitelist: 0.0.0.0/0 (for now) >> Copy connection string
1. `nano .env`
    1. `MONGO_URI=mongodb+srv://TravelAdmin:<db_password>@clustersuhail.pvtglfc.mongodb.net/?appName=ClusterSuhail`
    2. `PORT=3001`
#### Step 7: Start Backend with PM2
1. Start PM2 - `pm2 start index.js --name travelmemory-backend`
   <img width="1928" height="288" alt="image" src="https://github.com/user-attachments/assets/84c29ed5-831c-469a-89b9-5cc1fe64bd39" />
2. Save - `pm2 save`
   <img width="1216" height="122" alt="image" src="https://github.com/user-attachments/assets/88c9a9bf-d7bd-49b2-b67e-e80d4641a4c6" />
3. Startup - `pm2 startup`
   <img width="1996" height="184" alt="image" src="https://github.com/user-attachments/assets/4857bf46-72e0-4ab4-8789-512e1eb66578" />
4. Status - `pm2 status`
   <img width="1570" height="204" alt="image" src="https://github.com/user-attachments/assets/c5a289ce-0abe-4d44-be29-1ffebf213091" />

#### Step 8: Configure Nginx Reverse Proxy
1. Edit default config: `sudo nano /etc/nginx/sites-available/default`
2. Replace inside server {} block:
   ```
   server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
   }
#### Step 9: Restart Nginx
1. `sudo nginx -t` <img width="1294" height="98" alt="image" src="https://github.com/user-attachments/assets/36323997-87aa-4f13-8ef7-47f432a6b953" />
2. `sudo systemctl restart nginx` <img width="2022" height="610" alt="image" src="https://github.com/user-attachments/assets/f00bb2d3-a6a9-4aee-90a4-3161d5c74d1c" />
#### Step 10: Test Backend
Open browser: `http://65.0.7.136`
#### Step 11: Insert Sample Data (Testing)
Use Postman or curl:
```
curl -X POST http://your-ec2-public-ip/api/trips \
-H "Content-Type: application/json" \
-d '{
    "tripName": "Incredible India",
    "startDateOfJourney": "19-03-2022",
    "endDateOfJourney": "27-03-2022",
    "nameOfHotels":"Hotel Namaste, Backpackers Club",
    "placesVisited":"Delhi, Kolkata, Chennai, Mumbai",
    "totalCost": 800000,
    "tripType": "leisure",
    "experience": "Lorem Ipsum...",
    "image": "https://t3.ftcdn.net/jpg/03/04/85/26/360_F_304852693_nSOn9KvUgafgvZ6wM0CNaULYUa7xXBkA.jpg",
    "shortDescription":"India is a wonderful country",
    "featured": true
}'
```
#### Step 12: Start Server
1. `node index.js` - <img width="1306" height="88" alt="image" src="https://github.com/user-attachments/assets/2ed9f673-c525-4ebf-9af0-17faf20aac45" />

## Task 2: Frontend and Backend Connection:
Open a new CMD and connect to the EC2 instance to start the front end part
1. For frontend, need to create .env file and put the following content (remember to change it based on your requirements): `REACT_APP_BACKEND_URL=http://localhost:3001`
   <img width="928" height="128" alt="image" src="https://github.com/user-attachments/assets/a23fdcc7-444f-483a-a5b4-09b3fe1a69bc" />
2. Start frontend: `npm start` <img width="1054" height="366" alt="image" src="https://github.com/user-attachments/assets/0821b13b-321c-49a1-a4b8-fc44fc7a5d15" />
3. Test if the page is working in browser using the URL: <img width="3982" height="1802" alt="image" src="https://github.com/user-attachments/assets/bc17e3bf-7ac3-4d19-907f-8052243b8712" /> <img width="975" height="384" alt="image" src="https://github.com/user-attachments/assets/334cb573-9efe-4067-ae39-2cb50125609f" />


## Task 3: Scaling the Application:
#### STEP 1: Create AMI (Golden Image) - This captures your working server.
In AWS Console:
1. Go to EC2 → Instances
2. Select your working instance
3. Click Actions → Image and templates → Create Image
    Fill:
1. Name: travelmemory-ami
2. Leave defaults
<img width="1390" height="1362" alt="image" src="https://github.com/user-attachments/assets/ff2b34fd-f33b-4529-9885-9afd6104b444" />

#### STEP 2: Create Launch Template
1. Go to EC2 → Launch Templates
2. Click Create Launch Template
   ```
   Fill:
   Basic:
    Name: travelmemory-template
   AMI:
    Select your created AMI
   Instance Type:
    t2.micro
   Key Pair:
    Select your key
   Security Group:
   Create/select one with:
   1. HTTP → 80 → 0.0.0.0/0
   2. SSH → 22 → Your IP
`` <img width="1526" height="1496" alt="image" src="https://github.com/user-attachments/assets/c908696b-6177-400f-8099-2f06c3708daf" />

#### STEP 3: Create Target Group
1. Go to EC2 → Target Groups
2. Click Create target group
    1. Settings:
        1. Type: Instances
        2. Protocol: HTTP
        3. Port: 80
        4. Name: travelmemory-tg
    2. Health Check:
        1. Path: /
        2. Port: traffic port
    3. Click Next → Create
       <img width="1482" height="1100" alt="image" src="https://github.com/user-attachments/assets/2ad8e3b5-7683-49f9-a783-4ae043d369b7" />

#### STEP 4: Create Load Balancer
1. Go to EC2 → Load Balancers → Create
2. Choose:
    1. Application Load Balancer
3. Configure:
    1. Basic:
        1. Name: travelmemory-alb
        2. Scheme: Internet-facing
    2. Network:
        1. Select 2 or more subnets (VERY IMPORTANT)
    3. Security Group:
        1. Allow:
            1. HTTP (80) → 0.0.0.0/0
        2. Listener:
            1. Protocol: HTTP
            2. Forward to → travelmemory-tg
4. Click Create Load Balancer
   <img width="1478" height="1246" alt="image" src="https://github.com/user-attachments/assets/37dad95c-e4b4-4c29-81ea-382f174ddcfb" />

#### STEP 5: Create Auto Scaling Group
1. Go to EC2 → Auto Scaling Groups → Create
2. Choose Launch Template
    1. Select: travelmemory-template
3. Network
    1. Choose same VPC
    2. Select 2 subnets (multi-AZ)
4. Attach Load Balancer
    1. Select:
        1. Existing Load Balancer
        2. Choose travelmemory-alb
        3. Select Target Group: travelmemory-tg
5. Group Size
    1. Set:
        1. Desired: 2
        2. Min: 2
        3. Max: 4
6. Scaling Policy
    1. Choose:
        1. Target tracking
    2. Set:
        1. CPU Utilization: 50%
7. Create ASG
   <img width="2900" height="1584" alt="image" src="https://github.com/user-attachments/assets/0525f05c-6a1c-40b0-afdc-b6dcc58b45d1" />

#### STEP 6: Verify is multiple instances are running as per the scaling group
<img width="3566" height="308" alt="image" src="https://github.com/user-attachments/assets/6001b0e9-0f23-4408-a882-51a82fd09b37" />

## Task 4. Domain Setup with Cloudflare:
#### Step 1: Buying a domain
1. Bought a domain from namecheap - `travelmemoryapp.xyz`
#### Step 2: Add Domain to Cloudflare
1. Login to: Cloudflare Dashboard
2. Click:
    1. Add Site
3. Enter domain:
    1. travelmemoryapp.xyz
4. Choose:
    1. Free Plan
<img width="2674" height="206" alt="image" src="https://github.com/user-attachments/assets/bd5c3990-102f-4b1c-bb0d-172a24b07f49" />

#### STEP 3: Create CNAME Record
1. Go to: DNS → Records → Add Record
    1. Create: `CNAME` `www` `travelmemory-alb-184073069.ap-south-1.elb.amazonaws.com`
#### STEP 4: Create A Record (Frontend EC2)
1. Add another record: `A` `@` `13.203.155.187` <img width="975" height="145" alt="image" src="https://github.com/user-attachments/assets/38b86655-b122-496d-99af-8a2b523f0392" />
2. Continue to activation
#### STEP 5: Change Nameservers
1. Cloudflare will give nameservers like:
    1. `leanna.ns.cloudflare.com`
    2. `ricardo.ns.cloudflare.com`
2. Go to Your Domain Registrar: Replace existing nameservers with Cloudflare nameservers.

#### After completing the domain and cloudflare part, the site will be accessable from the domain - `www.travelmemoryapp.xyz` or `https://travelmemoryapp.xyz/addexperience`

## Task 5: Design a deployment architecture diagram using [draw.io](https://www.draw.io/) to visualize the flow and connections.
<img width="928" height="1730" alt="image" src="https://github.com/user-attachments/assets/2054fe96-d611-448b-82e8-a925a8c917b0" />
<img width="1536" height="1024" alt="Deployment Architecture Diagram" src="https://github.com/user-attachments/assets/6b547cfe-046d-440c-85fa-2352cfb6d453" />
