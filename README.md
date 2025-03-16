## 1. Install tools
1. Install AWS CLI
2. Install helm
    ````
    winget install Helm.Helm
    ````

3. Install EKSCTL (Windows)
    ````
    winget install --id=eksctl.eksctl  -e
    ````

## 2. Configure Credentials
1. Log into AWS Console with a user with the right policy to create users (Normally root/admin user)
2. Go to IAM (Search Bar -> IAM)
3. Select Users (Left hand side)
4. Create User
   1. Enter a username (IE: eks-demo)
   2. Check "Provide Access to the AWS Console" check-box
   3. Attach Policy (AdministratorAccess for this demo)
   4. Review and Create User

## 3. Create Resources
NOTE 1: Replace Variables \
NOTE 2: When creating the cluster with EKSCTL command, do not include the word cluster in the cluster namd.
   EKSCTL will add the word cluster in the names

1. Create EKS Cluster using EKSCTL
````
eksctl create cluster --name=<CLUSTER_NAME> --region=<DEFAULT_REGION> --zones=<ZONE1>,<ZONE2> --without-nodegroup 
````
This command will deploy a couple of CloudFormation Stacks, including VPC, IAM Instance roles. \
DEMO COMMAND
````
eksctl create cluster --name=delafuent3-eks-demo --region=ap-southeast-2 --zones=ap-southeast-2a,ap-southeast-2b --without-nodegroup
````


2. Create IAM OIDC Provider
This will allow our EKS Cluster to user Service Accounts.
````
eksctl utils associate-iam-oidc-provider --region <REGION> --cluster <CLUSTER_NAME> --approve
````
DEMO COMMAND
````
eksctl utils associate-iam-oidc-provider --region ap-southeast-2 --cluster delafuent3-eks-demo --approve
````

3. Create NodeGroup
Powershell
````
# Create Public Node Group   
eksctl create nodegroup --cluster=delafuent3-eks-cluster `
                       --region=us-east-1 `
                       --name=eksdemo1-ng-public1 `
                       --node-type=t3.medium `
                       --nodes=2 `
                       --nodes-min=2 `
                       --nodes-max=4 `
                       --node-volume-size=20 `
                       --ssh-access `
                       --ssh-public-key=kube-demo `
                       --managed `
                       --asg-access `
                       --external-dns-access `
                       --full-ecr-access `
                       --appmesh-access `
                       --alb-ingress-access 
````
Linux
````
# Create Public Node Group   
eksctl create nodegroup --cluster=eksdemo1 \
                       --region=us-east-1 \
                       --name=eksdemo1-ng-public1 \
                       --node-type=t3.medium \
                       --nodes=2 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \
                       --ssh-access \
                       --ssh-public-key=kube-demo \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access 
````




## 4. Review deployed resources



## 5. Clean Up
1. Delete Nodegroup
````
eksctl delete nodegroup --cluster=delafuent3-eks-cluster  --name=eksdemo1-ng-public1 --disable-eviction
````

1. Delete Cluster
````
eksctl delete cluster --name=<CLUSTER_NAME>
````


