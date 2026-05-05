# Travel Memory Application Deployment
The Travel Memory application has been developed using the MERN stack

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
1. For frontend, need to create .env file and put the following content (remember to change it based on your requirements): `REACT_APP_BACKEND_URL=http://localhost:3001`
   <img width="928" height="128" alt="image" src="https://github.com/user-attachments/assets/a23fdcc7-444f-483a-a5b4-09b3fe1a69bc" />
2. Start frontend: `npm start` <img width="1054" height="366" alt="image" src="https://github.com/user-attachments/assets/0821b13b-321c-49a1-a4b8-fc44fc7a5d15" />
3. Test if the page is working in browser using the URL: <img width="3982" height="1802" alt="image" src="https://github.com/user-attachments/assets/bc17e3bf-7ac3-4d19-907f-8052243b8712" />

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
    Settings:
        1. Type: Instances
        2. Protocol: HTTP
        3. Port: 80
        4. Name: travelmemory-tg
    Health Check:
        1. Path: /
        2. Port: traffic port
    Click Next → Create
