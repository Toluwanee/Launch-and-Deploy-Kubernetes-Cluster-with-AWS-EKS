<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# 2 - Set Up Kubernetes Deployment

**Project Link:** [View Project](https://nextwork.ai/projects/ca1e2f05-dd07-5e77-b83c-1e6a6516944c)

**Author:** Toluwalope Oni  
**Email:** toluwalope9@gmail.com

---

![Image](https://nextwork.ai/compassionate_orange_mysterious_tuke/uploads/ca1e2f05-dd07-5e77-b83c-1e6a6516944c_45e6c3de5)

## Introducing Today's Project!

In this project, I will setup a Kubernetes deployment.  I'll pull a developed app's backend from GitHub and prepare it for deployment with Kubernetes.

### Tools and concepts

I used Amazon EKS, Git,  Amazon ECR, Docker... 
Git: Used to clone the backend application repository from GitHub onto your workstation.
Docker: Used to package my application code, dependencies, and environment configurations into a single, consistent container image using a Dockerfile.
Amazon ECR: Served as a secure, cloud-based registry to store and manage your built container images.
Amazon EC2: Acted as my development workstation where you ran commands, installed Docker, built the image, and pushed it to ECR.
Amazon EKS: Your managed Kubernetes cluster on AWS, which will use the image in ECR to deploy and manage your app.

Key Steps Accomplished
Set up and Access your EC2 workstation: Logged in and verified your connection to Amazon EKS.

Pull the Application Code

Prepare and Install Docker

Build the Container Image

Create ECR Registry & Authenticate

Tag and Push the Image

### Project reflection

This project took me approximately 2hrs... The most challenging part was managing money and time - I left the project running overnight... My favourite part was authenticating the EC2 instance with the ECR registry.

Something new that I learnt from this experience was I need to authenticate with the ECR with the EC2 I am pushing the image from, the project.

## What I'm deploying

To set up today's project, I launched a Kubernetes cluster. Steps I took to do this included:

Launching an EC2 instance running Amazon Linux 2023 to serve as my secure management workstation.

Installing eksctl, the official command-line tool, to simplify and automate cluster creation.

Creating and attaching an IAM role (nextwork-eks-instance-role) with AdministratorAccess to grant my EC2 workstation the necessary permissions to provision AWS resources.

Running the eksctl create cluster command to provision an Amazon EKS cluster named nextwork-eks-cluster with a managed node group of t3.micro instances.

### I'm deploying an app's backend

I retrieved the backend that I plan to deploy. I retrieved backend code by using the git clone command - git clone <HTTPS URL>
to clone my team member's repository.

An app's backend means the "brain" of an application. It's where the main work is done - where our app processes user requests and stores and retrieves data. Unlike frontend code, which is what users see and interact with, backend code works on the server side (i.e. in the background) to make sure your app behaves as expected (e.g. loads a new page) when a user does things like clicking on buttons.

![Image](https://nextwork.ai/compassionate_orange_mysterious_tuke/uploads/ca1e2f05-dd07-5e77-b83c-1e6a6516944c_1ebb86c71)

## Building a container image

Once I cloned the backend code, my next step is to build a container image of the backend. This is because a container image lets Docker set up multiple, identical containers so your application runs consistently across different environments.
Kuberenetes does not work with backend code directly, it works with container images that are already built from backend code.

When I tried to build a Docker image of the backend, I ran into a permissions error:

with the command: docker build -t nextwork-flask-backend .
`ERROR: permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/_ping": dial unix /var/run/docker.sock: connect: permission denied`

because the commands I ran to install Docker installed it for the root account, so only the root user can talk to the Docker engine. BUT, I've actually set up this Instance Connect session with another user called ec2-user, so Docker commands are blocked.

The Docker commands you ran before this worked because they're prefixed with sudo, which lets a non-root user run commands with root user rights. But, it's good practice to give your ec2-user the permission instead of using sudo each time.

To solve the permissions error, I added the ec2-user to the docker group so that it allows the user to run docker commands without sudo everytime. The Docker group is a group in Linux systems that gives users the permission to run Docker commands.

![Image](https://nextwork.ai/compassionate_orange_mysterious_tuke/uploads/ca1e2f05-dd07-5e77-b83c-1e6a6516944c_45e6c3de5)

## Container Registry

I'm using Amazon ECR in this project to as a containter registry to safely and securely store, share and deploy container images. ECR is a good choice for the job because it's an AWS service, which lets Elastic Kubernetes Service (EKS) deploy your container image with minimal authentication setup.

Container registries like Amazon ECR are great for Kubernetes deployment because  thay can be used to securely store, share, and deploy container images.

Note: Kubernetes can start a container from any image it has access to - not just images in a repository.

The usual way is to pull the image from a registry like ECR or Docker Hub, because that keeps deployment simple: every instance in your Kubernetes cluster would use the same, up-to-date image on demand.

You could skip the registry and copy the image onto each instace by hand, but then you’d have to repeat that step every time the image changes (yikes, that's definitely a slower process). Using a registry frees you from that manual work, and keeps your cluster up to date automatically.

![Image](https://nextwork.ai/compassionate_orange_mysterious_tuke/uploads/ca1e2f05-dd07-5e77-b83c-1e6a6516944c_l2m3n4o5)

## EXTRA: Backend Explained

After reviewing the app's backend code, I've learnt that what the app is basically doing is:

My app's backend fetches data based on a search topic I enter.

The backend code uses Flask to connect with an external API (Hacker News Search API) and process the data.

The backend then sends the data back as formatted JSON.

### Unpacking three key backend files

The requirements.txt file lists the dependencies my application needs.

The Dockerfile gives Docker instructions on how to build the container image. Key commands in this Dockerfile include - FROM, WORKDIR, COPY, CMD

The app.py file contains the main code for my backend, it has 3 main parts.
1. Setting up the app and routing: The code starts my importing the flask framework and tools for creating the API. It defines a route (/contents/<topic>) that directs incoming requests to the SearchContents class. 

2. Fetching data: In the SearchContents class, the get() method extracts the topic from the URL, sends a request to the hacker news.

3. Sending the response: The collected data is turned into a formatted JSON response.
