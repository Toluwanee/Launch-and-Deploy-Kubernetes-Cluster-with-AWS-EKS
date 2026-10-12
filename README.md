<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# 1
# Launch a Kubernetes Cluster

**Project Link:** [View Project](https://nextwork.ai/projects/aa8525df-82a0-5697-8721-a03b2db2c635)

**Author:** Toluwalope Oni  
**Email:** toluwalope9@gmail.com

---

![Image](https://nextwork.ai/compassionate_orange_mysterious_tuke/uploads/aa8525df-82a0-5697-8721-a03b2db2c635_e5f6g7h8)

## Introducing Today's Project!

In this project, I will launch a Kubernetes Cluster with AWS EKS to understand how it works

## What is Kubernetes?

Kubernetes is a container orchestration platform that coordinates containers so that they can run smoothly. It’s the standard tool for keeping large, container-based applications steady and easy to scale with traffic. 

Companies and developers use Kubernetes to manage their application without causing downtime.

I used eksctl to run eks cluster. The create cluster command: 
eksctl create cluster \
--name nextwork-eks-cluster \
--nodegroup-name nextwork-nodegroup \
--node-type t3.micro \
--nodes 3 \
--nodes-min 1 \
--nodes-max 3 \
--version 1.33 \
--region your-region-code


What this command does:
Set up an EKS cluster named nextwork-eks-cluster
Launch a node group called nextwork-nodegroup.
Use t3.micro EC2 instances as nodes.
Start your node group with 3 nodes and automatically scale between 1 (minimum) and 3 nodes (maximum) based on demand.
Use Kubernetes version 1.31 for the cluster setup.

I initially ran into two errors while using eksctl. The first one was because eksctl was not installed, so I had to install it. The second one was because the EC2 machine in use does not have the IAM permission to create the eks cluster and permission has to be given to the EC2 instance that will be used.

![Image](https://nextwork.ai/compassionate_orange_mysterious_tuke/uploads/aa8525df-82a0-5697-8721-a03b2db2c635_ff9bfc221)

## eksctl and CloudFormation

CloudFormation helped to create my EKS cluster because CloudFormation stack helped to automate the creation of all the necessary resources for the EKS cluster which I would have done manually if I were to use AWS CLI

CloudFromation created VPC resources because networking is required within and outside the cluster. networking resources - VPC, subnets, route tables, security groups, NAT gateways and internet gateways!
These resources set up a private, secure network for your containers to connect with each other and the internet while keeping your app private.

There was also a second CloudFormation stack for the node group. The difference between a cluster and node group is that the cluster is that while the overall EKS cluster represents the complete system that combines the control plane (the managed "brain" that coordinates containers) with the nodes (the servers running them), the node group acts as a management wrapper around those nodes. Grouping these worker servers together allows me to easily manage and apply settings like instance types, resource limits, and software updates for the entire fleet simultaneously, rather than adjusting each node individually.

![Image](https://nextwork.ai/compassionate_orange_mysterious_tuke/uploads/aa8525df-82a0-5697-8721-a03b2db2c635_w3e4r5t6)

## The EKS console

I had to create an IAM access entry in order to grant my AWS Admin user the necessary internal permissions to see and interact with the nodes inside my EKS cluster. Initially, I could only see the node groups because AWS permissions do not automatically extend inside the Kubernetes cluster itself.

An access entry is like a secure handshake that connects AWS and Kubernetes. While IAM handles AWS's external logins and permissions, RBAC (Role-Based Access Control) governs internal Kubernetes security. The access entry maps my AWS IAM identity directly to a Kubernetes RBAC role, allowing the cluster to trust and authorize my actions.

I set it up by navigating to the Access tab on my cluster page in the Amazon EKS console, creating a new access entry for my IAM Admin user's Principal ARN, and associating the AmazonEKSClusterAdminPolicy with a cluster-wide access scope to give myself full administrative rights.

It took 15 to 20 minutes to provision on AWS to create my cluster. Since I'll create this cluster again in the next project of this series, maybe this process could be sped up if I keep it active so I can jump straight to deployment without waiting for a new cluster to build.

![Image](https://nextwork.ai/compassionate_orange_mysterious_tuke/uploads/aa8525df-82a0-5697-8721-a03b2db2c635_e5f6g7h8)

## EXTRA: Deleting nodes

Did you know you can find your EKS cluster's nodes in Amazon EC2? This is because in amazon when you create nodes in a Kubernetes cluster on AWS, each node is actually an EC2 instance!
Kubernetes uses a generic term (node) because different cloud platforms use different cloud resources to be the node; in AWS, EC2 instances are used as our nodes.

Desired size means the number of nodes you want running in your node group... Mininum size is the minimum number of nodes my node group needs to have running to keep my app running at all times and maximum sizes are helpful for scaling up node group from the desired size in high-demand periods....

When I deleted my EC2 instances they are deleted at first but they restart after being deleted. This is because Kubernetes automatically detects the change and launches new nodes to keep the cluster running at its desired state.

![Image](https://nextwork.ai/compassionate_orange_mysterious_tuke/uploads/aa8525df-82a0-5697-8721-a03b2db2c635_q7r8s9t0)

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/aa8525df-82a0-5697-8721-a03b2db2c635)*
