# Travel Memory Application Deployment
The Travel Memory application has been developed using the MERN stack

## Task 1. Backend Configuration: Clone the repository and navigate to the backend directory

#### Step 1: Launch EC2 Instance
1. Go to AWS Console → EC2
2. Click Launch Instance
3. Configure:
    1. AMI: Ubuntu 
    2. Instance type: t2.micro (free tier)
    3. Key pair: create/download
    4. Security Group:
         1. SSH → 22 (Your IP)
         2. HTTP → 80 (Anywhere)
#### Step 2: Connect to EC2
1. `ssh -i PEM_KEY.pem ubuntu@13.203.155.187`
   <img width="975" height="648" alt="image" src="https://github.com/user-attachments/assets/362ab5e8-abde-40ab-ac4f-07e910dc742b" />

#### Step 3: Install Required Software
1. `sudo apt update && sudo apt upgrade -y`
2. Install Node.js (LTS)
`curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`
`sudo apt install -y nodejs`
3. Install Git
`sudo apt install git -y`
4. Install Nginx
`sudo apt install nginx -y`
5. Install PM2 (process manager)
`sudo npm install -g pm2`
#### Step 4: Clone Repository
1. `git clone https://github.com/suhailm67-sys/Travel_Memory_App.git`
2. `cd Travel_Memory_App/backend`
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
2. Save - `pm2 save`
3. Startup - `pm2 startup`
4. Status - `pm2 status`
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


