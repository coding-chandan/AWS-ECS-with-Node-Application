AWS ECS – ECR Project with deploy nodejs application 

contanerize nodejs application with ECS AWS service , upload the image on ECR AWS service and help me to master this project 
note :- use aws ecs service for docker and image repo for ecr use fargate (serverless) create Dockerfile for nodejs application with nodejs application files in one directory in ubuntu linux install nodejs in ubuntu linux on ecr and ecs service help me with console in aws

1. Set Up Node.js on Ubuntu Linux

First, install Node.js on your Ubuntu system:

bash
sudo apt update
sudo apt install -y nodejs npm
node -v  # Check Node.js version
npm -v   # Check npm version

2. Create a Node.js Application

Navigate to your project directory:
bash

mkdir my-node-app && cd my-node-app
npm init -y

(optional)
Install Express.js (optional for a web app):
npm install express

Create an index.js file:
javascript

const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.send("Hello from Node.js on AWS ECS Fargate!");
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});

Modify package.json to update the start script:
json
"scripts": {
  "start": "node index.js"
}


3. Create a Dockerfile

Inside your project directory, create a Dockerfile:

Dockerfile

# Use official Node.js image
FROM node:18

# Set working directory
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy application files
COPY . .

# Expose the port the app runs on
EXPOSE 3000

# Start the application
CMD ["npm", "start"]



4. Build and Test the Docker Image
Run the following commands to build and test your image locally:

bash
docker build -t my-node-app .
docker run -p 3000:3000 my-node-app

Visit http://localhost:3000 in your browser to check if it's working.

•	Now create a repo on aws ecr and go to push commands 
•	Create a I am user and add policy as containerregistryfullaccess for login in aws from terminal
•	Afterthat build,tag,push-image to ecr aws


Step 5: Push Docker Image to AWS ECR

1. Install & Configure AWS CLI

bash
sudo apt install -y awscli
aws configure
Enter your:
•	AWS Access Key
•	AWS Secret Key
•	Region (e.g., us-east-1)


2. Create an ECR Repository

bash
aws ecr create-repository --repository-name node-app

3. Authenticate Docker to ECR

bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com

4. Tag and Push Image to ECR

Bash
docker tag node-app:latest <account-id>.dkr.ecr.us-east-1.amazonaws.com/node-app:latest
docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/node-app:latest
________________________________________


Step 6: Deploy the Image on AWS ECS (Fargate)

1. Create an ECS Cluster
•	Go to AWS Console > ECS.
•	Click Create Cluster.
•	Choose Networking only (Fargate) and create the cluster.

2. Create a Task Definition
•	Go to ECS > Task Definitions.
•	Click Create new Task Definition.
•	Choose Fargate.
•	Define: 
o	Container Name: node-app
o	Image: <account-id>.dkr.ecr.us-east-1.amazonaws.com/node-app:latest
o	Port Mapping: 3000
o	Memory & CPU: 0.5GB memory, 0.25 vCPU
•	Click Create Task Definition.

3. Create an ECS Service
•	Go to ECS > Clusters > Your Cluster.
•	Click Create Service.
•	Choose Launch Type: Fargate.
•	Select Task Definition.
•	Choose Number of Tasks: 1.
•	Configure Networking: 
o	VPC: Select default or create one.
o	Subnets: Select public subnets.
o	Security Group: Open port 3000.

4. Create an Application Load Balancer
•	Go to EC2 > Load Balancers.
•	Click Create Load Balancer > Application Load Balancer.
•	Choose: 
o	Internet-facing
o	Listener: HTTP (80)
o	Target Group: Create a new one for ECS.
•	Register your ECS instance.

5. Attach ALB to ECS Service
•	Go back to ECS Edit Service.
•	Add a Load Balancer and select ALB Target Group.
________________________________________

Step 7: Test the Deployment
Find the Load Balancer DNS Name in EC2 > Load Balancers and visit:
http://<ALB-DNS-Name>
You should see: "Hello from AWS ECS Fargate!"
________________________________________

Mastering This Project
•	Practice scaling the ECS service.
•	Automate the deployment with CI/CD using AWS CodePipeline.
•	Enable logging with AWS CloudWatch.
•	Secure the application using IAM roles.


