# Dockerized-Nodes.js-App
Introduction
In this project, I will containerize a Node.js application, push it to Docker Hub, and set up a CI/CD pipeline with GitHub Actions to automate the build and deployment process. I will use PowerShell and VS Code for the entire workflow. This project is a hands-on demonstration  DevOps skills.

Project Steps
1. Set Up Your Environment
Prerequisites:
Install Docker Desktop (Download)
Install VS Code (Download)
Install Git (Download)
Install Node.js & npm (Download)
Create a GitHub repository

Open PowerShell and verify installations:
using the commands below:
docker --version
git --version
node -v

2. Create a Node.js Application
Step 1: Initialize a Node.js project in VS Code
using the command below:
mkdir DockerCICDProject  
cd DockerCICDProject  
npm init -y  
This generates a package.json file.

Step 2: Install Express.js framework
using the coomands below:
npm install express


Step 3: Create server.js file
using the command below: 
New-Item -Path . -Name "server.js" -ItemType "file"


Open server.js in VS Code and add the following code:

const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

app.get('/', (req, res) => {
    res.send('Hello, Docker CI/CD with GitHub Actions!');
});

app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});


3. Dockerize the Application

Step 1: Create a Dockerfile
run the command below:
New-Item -Path . -Name "Dockerfile" -ItemType "file"


Add the following content:

# Use official Node.js image
FROM node:18

# Set working directory
WORKDIR /app

# Copy package.json and install dependencies
COPY package*.json ./
RUN npm install

# Copy application code
COPY . .

# Expose port
EXPOSE 3000

# Start the application
CMD ["node", "server.js"]



Step 2: Create a .dockerignore file

run the command below:
New-Item -Path . -Name ".dockerignore" -ItemType "file"


Add:

node_modules
npm-debug.log


Step 3: Build and Run the Docker Image

run the command below: 

docker build -t my-node-app .
docker run -p 3000:3000 my-node-app

Verify the app runs on http://localhost:3000.


4. Push the Docker Image to Docker Hub
Step 1: Login to Docker Hub

run this command: 
docker login


Step 2: Tag and Push the Image
run this command: 
docker tag my-node-app <your-dockerhub-username>/my-node-app:v1
docker push <your-dockerhub-username>/my-node-app:v1

5. Set Up CI/CD with GitHub Actions

Step 1: Create GitHub Actions Workflow
In your project, create .github/workflows/cicd.yml:
run this command:

mkdir -p .github/workflows
New-Item -Path .github/workflows -Name "cicd.yml" -ItemType "file"


Add the following YAML configuration:

name: CI/CD Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and Push Docker Image
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: <your-dockerhub-username>/my-node-app:latest

  deploy:
    runs-on: ubuntu-latest
    needs: build-and-push

    steps:
      - name: Deploy application (Example Step)
        run: echo "Deployment step (Implement based on your environment)"


6. Configure GitHub Secrets
Go to GitHub Repo → Settings → Secrets and Variables → Actions
Add the following secrets:
DOCKER_USERNAME = Your Docker Hub username
DOCKER_PASSWORD = Your Docker Hub password


7. Push Code to GitHub and Trigger CI/CD
Step 1: Initialize Git and Commit Code

git init  
git add .  
git commit -m "Initial commit - Dockerized app with CI/CD"  
git branch -M main  
git remote add origin https://github.com/your-username/repositoryname 
git push -u origin main  


Once pushed, GitHub Actions will build the image and push it to Docker Hub automatically.



