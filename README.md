# Blue Green Deployment NodejsApp

![image](https://user-images.githubusercontent.com/54719289/113128063-51d5d680-9211-11eb-9b8f-17c2b8a041d7.png)


# Pre-requisites:
    - Install GIT
    - EKS Cluster
    - Install ALB-Ingress-Controller
    - Request a Cerficate using Certificate Manager
    - Create Hosted Zone with our Domain Name
    - External DNS Setup
# EKS Cluster Setup:
  [EKS Cluster Setup](https://github.com/Naresh240/eks-cluster-setup/blob/main/README.md)
# ALB Ingress Controller Setup:
  [ALB Ingress Controller](https://github.com/Naresh240/ALB-Ingress-Controller-Setup/blob/main/README.md)
  
# Create Hosted Zone with our Domain Name

![image](https://user-images.githubusercontent.com/54719289/113162461-3b427600-9237-11eb-9d44-1e5599393457.png)
![image](https://user-images.githubusercontent.com/54719289/113162140-edc60900-9236-11eb-82c5-9f101572d520.png)
![image](https://user-images.githubusercontent.com/54719289/113163637-41852200-9238-11eb-9df2-1ac7029e3ef5.png)


# Request a Cerficate using Certificate Manager
    In AWS certificate Manager:
![image](https://user-images.githubusercontent.com/54719289/113163816-6d080c80-9238-11eb-81b5-39d15bf89a63.png)
![image](https://user-images.githubusercontent.com/54719289/113164046-ab9dc700-9238-11eb-8cbf-fe513be44e41.png)
![image](https://user-images.githubusercontent.com/54719289/113164129-be180080-9238-11eb-9290-7169cfa5b60f.png)
![image](https://user-images.githubusercontent.com/54719289/113164196-ccfeb300-9238-11eb-941e-d9836eee2478.png)
Create Route53:
![image](https://user-images.githubusercontent.com/54719289/113164397-f8819d80-9238-11eb-8bcb-b74653f6d0e0.png)
![image](https://user-images.githubusercontent.com/54719289/113164472-09caaa00-9239-11eb-8537-8b21d0114ff4.png)

REfresh and check hostedzone:
![image](https://user-images.githubusercontent.com/54719289/113164717-426a8380-9239-11eb-95d2-e7d5c1a93f29.png)

![image](https://user-images.githubusercontent.com/58024415/94990930-301ad000-059d-11eb-9c5d-8ee47d494f82.png)

Note : ARN for certificate : 	arn:aws:acm:us-east-1:136962450893:certificate/c4e6bf26-02df-4e49-b88b-9412562c1966

# External DNS Setup:
  [External DNS](https://github.com/Naresh240/External-DNS-Setup-Kubernetes/tree/main)
  
![image](https://user-images.githubusercontent.com/54719289/113166169-6aa6b200-923a-11eb-857b-d0064175b0f6.png)

    
# Install GIT:
    yum install git -y
# Install npm:
    sudo yum install -y gcc-c++ make
    curl -sL https://rpm.nodesource.com/setup_13.x | sudo -E bash -
    sudo yum install -y nodejs
# Install Docker:
    yum install docker -y
    service docker start
# Clone code from github:
    git clone https://github.com/Naresh240/RollingUpdate-Rollback-NodejsApp
    cd RollingUpdate-Rollback-NodejsApp
# Build Maven Artifact:
    npm install
# Build Docker image for Springboot Application
    docker build -t naresh240/nodejs-k8s:v1 .
# Docker login
    docker login
# Push docker image to dockerhub
    docker push naresh240/nodejs-k8s:v1
# Deploy nodejs Application using below commands:
    kubectl apply -f deployment.yml

![image](https://user-images.githubusercontent.com/54719289/113134274-80a37b00-9218-11eb-9961-7f6a850a6331.png)

    kubectl apply -f service.yml

![image](https://user-images.githubusercontent.com/54719289/113134368-9b75ef80-9218-11eb-968b-4621621d4e93.png)

# Check pods and services:
    kubectl get pods
    kubectl get svc
![image](https://user-images.githubusercontent.com/54719289/113134586-e4c63f00-9218-11eb-8981-41cb5dfca5b3.png)

# Run ingress for checking output with DNS name
    kubectl apply -f ingress.yml
    
without externaldns and security:

# Check Load Balancer of ALB ingress controller attached to ingress or not
    kubectl get ingress
    
 ![image](https://user-images.githubusercontent.com/54719289/113135384-fb20ca80-9219-11eb-818d-f21b56a2b45b.png)
 
    #remove paths for ssl and execute it again (kubectl apply -f ingress.yml and kubectl get ingress)
 ![image](https://user-images.githubusercontent.com/54719289/113136995-e6453680-921b-11eb-846b-9b83e41efa23.png)

  Now another load balance is created 
![image](https://user-images.githubusercontent.com/54719289/113137135-096fe600-921c-11eb-9e5f-78519208c313.png)
![image](https://user-images.githubusercontent.com/54719289/113137176-18ef2f00-921c-11eb-8c03-2573ada4122f.png)



# Go to UI and check our external dns, which showing output application with HTTPS
  http://nodejs.staticwebsitehosting.tk/
  
![image](https://user-images.githubusercontent.com/54719289/113163166-ce7bab80-9237-11eb-83f5-bed4fe7b3700.png)

# Upgrading for nodejs Application:
Edit our our application and Build docker image with new tag:
    
    docker build -t naresh240/nodejs-k8s:v2 .

Push Docker image to docker hub with tag v2:

    docker push naresh240/nodejs-k8s:v2

upgrade nodejs application with tag v2:
    
    kubectl rollout history deployment nodejs-deployment
    
Check rollout history for revision "1"
    
    kubectl rollout history deployment nodejs-deployment --revision=1
    
Upgrade new image using below command
    
    kubectl set image deployment nodejs-deployment nodejs-deployment=naresh240/nodejs-k8s:v2
    
# Goto Web UI and check updated version output
![image](https://user-images.githubusercontent.com/58024415/95006858-854ef400-0626-11eb-8250-9a5d4a559e11.png)

Check rollout history for revision "2"

    kubectl rollout history deployment nodejs-deployment --revision=2
  
 Rollout to previous version using below command 
    
    kubectl rollout undo deployment nodejs-deployment --to-revision=1
    
# Goto Web UI and check again:
  https://nodejs.cloudtechmasters.ml/
  
![image](https://user-images.githubusercontent.com/58024415/95006993-fa6ef900-0627-11eb-8269-66299b56f504.png)

# After updating the domain in ingress.yml, run the ingress.yml
![image](https://user-images.githubusercontent.com/54719289/113166377-a04b9b00-923a-11eb-8107-b72d0216a2f4.png)

