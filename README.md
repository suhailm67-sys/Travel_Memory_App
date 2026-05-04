# Travel Memory Application Deployment
The Travel Memory application has been developed using the MERN stack

## Task 1. Backend Configuration:

### Clone the repository and navigate to the backend directory

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
