# Setup Kubernetes on Amazon EKS

You can follow same procedure in the official  AWS document [Getting started with Amazon EKS – eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)   

#### Pre-requisites: 
  - Create an EC2 Instance with security group on port 22
  - <img width="1273" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/4569bf11-cc72-42a3-a000-1571cbb26bc8">

  - Make sure to login as a root user with the following command
    ```
    sudo su -
    ```
    <img width="725" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/56011cf3-914e-4ee0-835e-1551f1b04c86">


  - Install AWSCLI latest verison

  - <img width="1254" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/be0f5196-86a2-4781-8913-2c51ed4daf56">

  - You should be able to see the latest version
    <img width="697" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/d64cb88a-530f-4681-bcbf-f3b3cf9e4b25">



1. Setup Kubectl   
   a. Download Kubectl version 1.21  
   b. Grant execution permissions to Kubectl executable   
   c. Move kubectl onto /usr/local/bin   
   d. Test that your Kubectl installation was successful    

   ```sh 
   curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.21.2/2021-07-05/bin/linux/amd64/kubectl
   chmod +x ./kubectl
   mv ./kubectl /usr/local/bin 
   kubectl version --short --client
   ```
   <img width="646" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/2650ebd0-555f-41d9-bc10-719f7db21ff1">


   
2. Setup eksctl   
   a. Download and extract the latest release   
   b. Move the extracted binary to /usr/local/bin   
   c. Test that your eksclt installation was successful   

   ```sh
   curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
   sudo mv /tmp/eksctl /usr/local/bin
   eksctl version
   ```
  <img width="1266" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/d93d4366-626a-405f-9f7d-8c07334dc8e2">

3. Create an IAM Role and attache it to EC2 instance
    IAM user should have access to
   
    IAM
   
    EC2
   
    CloudFormation
   
    AdministratorAccess (Do not grant this access in the real world)
   
 Note: Check eksctl documentaiton for [Minimum IAM policies](https://eksctl.io/usage/minimum-iam-policies/)

   <img width="996" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/f9005c92-8699-40e8-bc00-4c54897a0f3f">

4. Attach the IAM role to your EC2 bootstrap
   
   <img width="1273" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/dd5062ec-c8a8-4d8e-88bb-3e07539ff7f4">

Add the IAM role and update

<img width="1252" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/2e8a35c0-ad71-416e-9f54-48649bff1acd">

6. Create your cluster and nodes 

```
eksctl create cluster --name nicholas213  \
region ap-northeast-1 \
node-type t2.small \
   

7. To delete the EKS clsuter 
   ```sh 
   eksctl delete cluster nicholas213 --region ap-northeast-1
   ```
   
8. Validate your cluster using by creating by checking nodes and by creating a pod 
   ```sh 
   kubectl get nodes
   kubectl run tomcat --image=tomcat 
   ```
   
   #### Deploying Nginx pods on Kubernetes
1. Deploying Nginx Container
    ```sh
    kubectl create deployment  demo-nginx --image=nginx --replicas=2 --port=80
    # kubectl deployment regapp --image=valaxy/regapp --replicas=2 --port=8080
    kubectl get all
    kubectl get pod
   ```

1. Expose the deployment as service. This will create an ELB in front of those 2 containers and allow us to publicly access them.
   ```sh
   kubectl expose deployment demo-nginx --port=80 --type=LoadBalancer
   # kubectl expose deployment regapp --port=8080 --type=LoadBalancer
   kubectl get services -o wide
   ```

