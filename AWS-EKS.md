What is Amazon EKS?

Amazon Elastic Kubernetes Service (Amazon EKS) is a managed service that you can use to run Kubernetes on AWS without needing to install, operate, and maintain your own Kubernetes control plane or nodes. 
https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html 

Kubernetes nodes : 

EKS itself becomes the master node called the Control Plane and the EC2 instances become the worker nodes called the Data Plane where the actual container images run.

How to create AWS EKS? 

*Create an AWS EC2 instance and install the eks on the instance*

Launch using eksctl 

*Install an AWS EC2 instance and proceed the EKS installation using the CLI terminal. 
 
<Prerequisite> 
1. Install the eksctl command line utility

  
2. Install the kubectl command line utility 
  


