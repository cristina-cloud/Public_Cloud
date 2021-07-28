What is Amazon EKS?

Amazon Elastic Kubernetes Service (Amazon EKS) is a managed service that you can use to run Kubernetes on AWS without needing to install, operate, 
and maintain your own Kubernetes control plane or nodes. 
https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html 



Kubernetes nodes : 
EKS 자체가 Control Plane이라는 마스터 노드가 되고 EC2 인스턴스가 실제 컨테이너 이미지가 실행되는 Data Plane 이라는 워커노드가 되는 형식으로 작동함 

따라서 EKS를 사용하게 되면 인스턴스를 따로 생성하여 워커노드를 사용할 수 있음 


How to create AWS EKS? 

*Create an AWS EC2 instance and install the eksctl on the instance*

Launch using eksctl 


