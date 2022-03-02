# What is Amazon EKS? #

Amazon Elastic Kubernetes Service (Amazon EKS) is a managed service that you can use to run Kubernetes on AWS without needing to install, operate, and maintain your own Kubernetes control plane or nodes. 

https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html 

#### Kubernetes nodes: #### 

EKS itself becomes the master node called the Control Plane and the EC2 instances become the worker nodes called the Data Plane where the actual container images run.

### How to create AWS EKS? ###

###### *Create an AWS EC2 instance and install the eks on the instance ######
###### *Install an AWS EC2 instance and proceed the EKS installation using the CLI terminal ###### 
 
## <Prerequisites> ##
https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html

#### 1. Install the eksctl command line utility ###
```
[ec2-user@ip-xxxxxxxxx ~]$ curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
[ec2-user@ip-xxxxxxxxx ~]$ sudo mv /tmp/eksctl /usr/local/bin/
[ec2-user@ip-xxxxxxxxx ~]$ eksctl version
0.58.0
```
 
### 2. Install the kubectl command line utility ###
```
[ec2-user@ip-xxxxxxxxxx ~]$ curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.20.4/2021-04-12/bin/linux/amd64/kubectl
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 38.3M  100 38.3M    0     0  8705k      0  0:00:04  0:00:04 --:--:-- 9516k
[ec2-user@ip-xxxxxxxxx ~]$
[ec2-user@ip-xxxxxxxxx ~]$ chmod +x ./kubectl
[ec2-user@ip-xxxxxxxxx ~]$
[ec2-user@ip-xxxxxxxxx ~]$ mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin 


[ec2-user@ip-xxxxxxxxx ~]$ kubectl version --short --client
Client Version: v1.20.4-eks-6b7464
```
 
### 3. Install aws-iam-authenticator ###
```
[ec2-user@ip-xxxxxxxxx ~]$ curl -o aws-iam-authenticator https://amazon-eks.s3-us-west-2.amazonaws.com/1.21.2/2021-07-05/bin/linux/amd64/aws-iam-authenticator
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 33.6M  100 33.6M    0     0  10.9M      0  0:00:03  0:00:03 --:--:-- 10.9M

[ec2-user@ip-xxxxxxxxx ~]$ chmod +x ./aws-iam-authenticator

[ec2-user@ip-xxxxxxxxx ~]$ mkdir -p $HOME/bin && cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$PATH:$HOME/bin

[ec2-user@ip-xxxxxxxxx ~]$ echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc
```
-> Amazon EKS uses IAM to provide authentication to your Kubernetes cluster through the AWS IAM authenticator for Kubernetes.
https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html
 
### 4. AWS configure ###
###### *Before proceeding this process, ass a new user and permission fo EKS ######
 
```
[ec2-user@ip-xxxxxxxxx ~]$ aws configure
AWS Access Key ID [None]: xxxxxxxxxxxxxxxxxxxxxxxx
AWS Secret Access Key [None]: xxxxxxxxxxxxxxxxxxxxxxxxx
Default region name [None]: ap-northeast-2
Default output format [None]:
``` 
-> Create a new user to manage EKS using IAM service 
 
-> Access key ID and secret access key are required for AWS cli to set up credentials to execute commands for building EKS service
 
## <Install EKS cluster and nodes> ##
```
[ec2-user@ip-xxxxxxxxx ~]$ eksctl create cluster --name test --region ap-northeast-2 --with-oidc --ssh-access --ssh-public-key eks-test --managed
2021-07-28 08:08:29 [ℹ]  eksctl version 0.58.0
2021-07-28 08:08:29 [ℹ]  using region ap-northeast-2
2021-07-28 08:08:29 [ℹ]  setting availability zones to [ap-northeast-2b ap-northeast-2a ap-northeast-2c]
2021-07-28 08:08:29 [ℹ]  subnets for ap-northeast-2b - public:xxxxxxxxx private:xxxxxxxxx
2021-07-28 08:08:29 [ℹ]  subnets for ap-northeast-2a - public:xxxxxxxxx private:xxxxxxxxx
2021-07-28 08:08:29 [ℹ]  subnets for ap-northeast-2c - public:xxxxxxxxx private:xxxxxxxxx
2021-07-28 08:08:29 [ℹ]  nodegroup "ng-eaa84246" will use "" [AmazonLinux2/1.20]
2021-07-28 08:08:29 [ℹ]  using EC2 key pair %!q(*string=<nil>)
2021-07-28 08:08:29 [ℹ]  using Kubernetes version 1.20
2021-07-28 08:08:29 [ℹ]  creating EKS cluster "test" in "ap-northeast-2" region with managed nodes
2021-07-28 08:08:29 [ℹ]  will create 2 separate CloudFormation stacks for cluster itself and the initial managed nodegroup
2021-07-28 08:08:29 [ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=ap-northeast-2 --cluster=test'
2021-07-28 08:08:29 [ℹ]  CloudWatch logging will not be enabled for cluster "test" in "ap-northeast-2"
2021-07-28 08:08:29 [ℹ]  you can enable it with 'eksctl utils update-cluster-logging --enable-types={SPECIFY-YOUR-LOG-TYPES-HERE (e.g. all)} --region=ap-northeast-2 --cluster=test'
2021-07-28 08:08:29 [ℹ]  Kubernetes API endpoint access will use default of {publicAccess=true, privateAccess=false} for cluster "test" in "ap-northeast-2"
2021-07-28 08:08:29 [ℹ]  2 sequential tasks: { create cluster control plane "test", 3 sequential sub-tasks: { 4 sequential sub-tasks: { wait for control plane to become ready, associate IAM OIDC provider, 2 sequential sub-tasks: { create IAM role for serviceaccount "kube-system/aws-node", create serviceaccount "kube-system/aws-node" }, restart daemonset "kube-system/aws-node" }, 1 task: { create addons }, create managed nodegroup "ng-eaa84246" } }
2021-07-28 08:08:29 [ℹ]  building cluster stack "eksctl-test-cluster"
2021-07-28 08:08:30 [ℹ]  deploying stack "eksctl-test-cluster"
2021-07-28 08:09:00 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:09:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:10:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:11:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:12:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:13:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:14:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:15:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:16:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:17:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:18:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:19:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:20:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:21:30 [ℹ]  waiting for CloudFormation stack "eksctl-test-cluster"
2021-07-28 08:25:32 [ℹ]  building iamserviceaccount stack "eksctl-test-addon-iamserviceaccount-kube-system-aws-node"
2021-07-28 08:25:32 [ℹ]  deploying stack "eksctl-test-addon-iamserviceaccount-kube-system-aws-node"
2021-07-28 08:25:32 [ℹ]  waiting for CloudFormation stack "eksctl-test-addon-iamserviceaccount-kube-system-aws-node"
2021-07-28 08:25:48 [ℹ]  waiting for CloudFormation stack "eksctl-test-addon-iamserviceaccount-kube-system-aws-node"
2021-07-28 08:26:05 [ℹ]  waiting for CloudFormation stack "eksctl-test-addon-iamserviceaccount-kube-system-aws-node"
2021-07-28 08:26:05 [ℹ]  serviceaccount "kube-system/aws-node" already exists
2021-07-28 08:26:05 [ℹ]  updated serviceaccount "kube-system/aws-node"
2021-07-28 08:26:05 [ℹ]  daemonset "kube-system/aws-node" restarted
2021-07-28 08:28:06 [ℹ]  building managed nodegroup stack "eksctl-Samseong-nodegroup-ng-eaa84246"
2021-07-28 08:28:06 [ℹ]  deploying stack "eksctl-Samseong-nodegroup-ng-eaa84246"
2021-07-28 08:28:06 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:28:22 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:28:39 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:28:55 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:29:12 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:29:28 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:29:45 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:30:01 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:30:18 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:30:38 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:30:55 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:31:13 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:31:31 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:31:46 [ℹ]  waiting for CloudFormation stack "eksctl-test-nodegroup-ng-eaa84246"
2021-07-28 08:31:46 [ℹ]  waiting for the control plane availability...
2021-07-28 08:31:46 [✔]  saved kubeconfig as "/home/ec2-user/.kube/config"
2021-07-28 08:31:46 [ℹ]  no tasks
2021-07-28 08:31:46 [✔]  all EKS cluster resources for "test" have been created
2021-07-28 08:31:46 [ℹ]  nodegroup "ng-eaa84246" has 2 node(s)
2021-07-28 08:31:46 [ℹ]  node "ip-xxxxxxxxxxxx.ap-northeast-2.compute.internal" is ready
2021-07-28 08:31:46 [ℹ]  node "ip-xxxxxxxxxxxx.ap-northeast-2.compute.internal" is ready
2021-07-28 08:31:46 [ℹ]  waiting for at least 2 node(s) to become ready in "ng-eaa84246"
2021-07-28 08:31:46 [ℹ]  nodegroup "ng-eaa84246" has 2 node(s)
2021-07-28 08:31:46 [ℹ]  node "ip-xxxxxxxxxxxx.ap-northeast-2.compute.internal" is ready
2021-07-28 08:31:46 [ℹ]  node "ip-xxxxxxxxxxxx.ap-northeast-2.compute.internal" is ready
2021-07-28 08:33:47 [ℹ]  kubectl command should work with "/home/ec2-user/.kube/config", try 'kubectl get nodes'
2021-07-28 08:33:47 [✔]  EKS cluster "test" in "ap-northeast-2" region is ready
```
-> It takes at least 15 to upto 30mins to get done and when you get a meesage that "EKS cluster "cluster name" in "region" is ready, then you are ready to go 
 
## <Verification> ##

### 1. Checking cluster nodes and Amazon EC2 nodes ###
```
 [ec2-user@ip-xxxxxxxxxxxx ~]$ kubectl get nodes -o wide
NAME                                                STATUS   ROLES    AGE     VERSION              INTERNAL-IP      EXTERNAL-IP    OS-IMAGE         KERNEL-VERSION                CONTAINER-RUNTIME
ip-xxxxxxxxxxxx.ap-northeast-2.compute.internal     Ready    <none>   5m38s   v1.20.4-eks-6b7464   xxxxxxxxxxxx     xxxxxxxxxxxx   Amazon Linux 2   5.4.129-63.229.amzn2.x86_64   docker://19.3.13
ip-xxxxxxxxxxxx.ap-northeast-2.compute.internal   Ready    <none>   5m34s   v1.20.4-eks-6b7464   xxxxxxxxxxxx   xxxxxxxxxxxx   Amazon Linux 2   5.4.129-63.229.amzn2.x86_64   docker://19.3.13
```

### 2. Checking current running Kubernetes system information on the cluster ###
```
[ec2-user@ip-xxxxxxxxxxxx ~]$ kubectl get pod --all-namespaces
NAMESPACE     NAME                      READY   STATUS    RESTARTS   AGE
kube-system   aws-node-9ddvp            1/1     Running   0          11m
kube-system   aws-node-nl8c9            1/1     Running   0          11m
kube-system   coredns-df796cc4d-dgz2f   1/1     Running   0          25m
kube-system   coredns-df796cc4d-jtrzx   1/1     Running   0          25m
kube-system   kube-proxy-6xn2t          1/1     Running   0          11m
kube-system   kube-proxy-c4xmk          1/1     Running   0          11m


or

[ec2-user@ip-xxxxxxxxxxxx ~]$ kubectl get pods -A
NAMESPACE     NAME                      READY   STATUS    RESTARTS   AGE
kube-system   aws-node-9ddvp            1/1     Running   0          14m
kube-system   aws-node-nl8c9            1/1     Running   0          14m
kube-system   coredns-df796cc4d-dgz2f   1/1     Running   0          27m
kube-system   coredns-df796cc4d-jtrzx   1/1     Running   0          27m
kube-system   kube-proxy-6xn2t          1/1     Running   0          14m
kube-system   kube-proxy-c4xmk          1/1     Running   0          14m
```


 

