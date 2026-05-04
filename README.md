# Travel Memory Application Deployment
The Travel Memory application has been developed using the MERN stack

## Task 1. Backend Configuration:

### Clone the repository and navigate to the backend directory

#### Step 1: Launch EC2 Instance
1. Go to AWS Console → EC2
2. Click Launch Instance
3. Configure:
    AMI: Ubuntu 
    Instance type: t2.micro (free tier)
    Key pair: create/download
    Security Group:
      SSH → 22 (Your IP)
      HTTP → 80 (Anywhere)
