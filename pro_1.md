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
```

The cloud formation has been completed successfully

<img width="1273" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/e76a5141-e28e-4a41-8f3d-e07a36aa40bf">



 Two new ec2 t2-micro instances has also been created:
   
<img width="1272" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/e458cfcd-d1cf-4ee9-962d-047fc3d10682">


   

8. Validate your cluster using by creating by checking nodes and by creating a pod 
  ```
kubectl get nodes
kubectl get all
kubectl run nickwebapp --image=httpd 
```
We have two nodes running when I execute **kubectl get nodes**

<img width="911" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/f43d36e2-2f0f-47e1-8441-486fc5f7a070">

We can see all the services running by executing **kubectl get all**

<img width="521" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/491af025-44e2-4671-bb0a-57cc3549ad6e">

I was also able to create a pod executing **kubectl run nickwebapp --image=httpd** 

<img width="717" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/83163e3f-6116-45cd-a393-8814d585802e">

 
   #### Deploying Nginx pods on Kubernetes
1. Deploying Nginx Container
    ```
    kubectl create deployment  demo-nginx --image=nginx --replicas=2 --port=80
    kubectl get all
    kubectl get pod
    ```
We have two pods running in our cluster    
<img width="488" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/9223f936-5d04-43ea-91cd-966cff5eef6f">


We can check how many replica set it has created **kubectl get replicaset**

<img width="421" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/69dca79d-d6db-4007-827a-00973435da92">


1. Expose the deployment as service. This will create an ELB in front of those 2 containers and allow us to publicly access them.
   ```sh
   kubectl expose deployment demo-nginx --port=80 --type=LoadBalancer
   # kubectl expose deployment regapp --port=8080 --type=LoadBalancer
   kubectl get services -o wide
   ```

   <img width="590" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/3ee4174e-fa29-495b-814c-02e01fba61cb">

We can verify from the AWS cloud to check the load balancer it has created.
<img width="1271" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/acca395b-25ea-4fdf-868a-8e5f954cdb17">

## Copy the DNS name of the LoadBalancer and paste it on a different tab ##
<img width="627" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/ea2f1d3a-7c7d-4e20-9fb9-07793375ae70">

## You should be able to see nginx default page ##
<img width="1278" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/69c149ca-9555-40ed-acbd-bd74adcde713">


## How to create a Manifest file to create a pod and a service ##

**First**, create a yml file: nosapod.yml and input your documentation from **Kubernetes api docmentation** 

<img width="231" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/23cb4c66-5acc-49c6-b8c6-8feb9fc6bab6">




**Next** we need to create a service file: service.yml

<img width="386" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/cec74e00-5ce9-476f-8455-0dd6bdddcb5a">

To create a pod with manifest file we need to run the command
```
kubectl apply -f nickspod.yml
```
<img width="463" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/333ea5c0-51dd-4cd7-9110-af745d53f7ec">

Apply
```
 kubectl apply -f service.yml
```
<img width="340" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/58870ee0-b7ab-47ab-b491-4796d9d1e470">


**LoadBalancer**
<img width="1280" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/358bfb51-6874-49b4-a84b-4a000e478123">


## Finally, Using labels and selectors ##

First, we need to make a few adjustments to our service.yml documentation. 
<img width="296" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/96f22220-782f-4ac0-bb3c-50f983254ccc">


Execute
```
kubectl apply -f service.yml
```
<img width="383" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/520c5de6-858e-4b58-93a5-001dd1c92e04">

We can go back and check our newly created load balancer from the AWS cloud 

<img width="1271" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/bf80da1c-cfed-4779-9963-7cb479dca485">

Copy the DNS name and paste it on a new tab:
<img width="1280" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/7606b7e0-8561-4d26-a911-1f553458c1b3">



**Finally:**
<img width="1276" alt="image" src="https://github.com/Nosa213/Devops_pro213/assets/125190958/10085607-e40a-44a5-93bb-9ab17da5a4d6">

**Make sure you do not forget to delete your EKS cluster to avoid charges using the command**.
```
eksctl delete cluster name --region ap-northeast-1
```













