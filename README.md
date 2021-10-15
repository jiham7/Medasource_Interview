# Deploy Kubernetes with API Endpoint into EKS with Terraform

Purpose: to deploy a cluster of Kubernetes applications with API endpoint into AWS EKS with the infrastructure script Terraform

Brief: 
1. Create a terraform project that can deploy a Kubernetes cluster into AWS with EKS. 
2. Deploy a simple HTTP service into Kubernetes. 
3. HTTP service will return "Hello world" for any GET requests
4. Setup autoscaler in Kubernetes

## Installation

Use the package manager [pip](https://pip.pypa.io/en/stable/) to install awscli.

```bash
pip install awscli 
```

Then install [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/)
and [Terraform CLI](https://learn.hashicorp.com/tutorials/terraform/install-cli)
 

## Scripting Resources with Terraform
Go into the terraform folder and run the following commands
```bash
#initialize Terraform and creates the state file (keeps track of resources deployed)
terraform init

# Checks to see that the syntax of the script is correct
terraform validate

# Performs a dry-run and shows a detailed summary of what will be deployed, changed, deleted
terraform plan

# Applies the changes after confirming "yes"
terraform apply
```

The deployment will take ~20min 

## Testing Cluster by deploying Simple App
We can now test the cluster by deploying a simple application onto it

Inside the terraform folder, run the following:
```bash
kubectl apply -f ../deployment.yaml
```
We then find the existing Pod by the command
```bash
kubectl get pods
```
Copy the name of your pod into this command
```bash
kubectl port-forward <Pod_Name> 8080:8080
```
This command connects the pod to the application and forwards all traffic to http://localhost:8080 on your local computer

But that's boring to have it all to yourself, how do we expose it to the web? 

We can use the code in service-loadbalancer.yaml, and you can run
```bash
kubectl apply -f service-loadbalancer.yaml
```
This command allows AWS to provision a Classic Load Balancer and attaches it to the Pod. It takes a while for the load balancer to be provisioned. 

We can then get the load balancer endpoint by the following command
```bash
kubectl describe service hello-kubernetes
```

Copy the link given in LoadBalancer Ingress and you will have deployed your application through Terraform onto AWS EKS.

Congrats!

## Deploying into Different Environments

Currently I have commented out the deployment of the staging and production environment in the terraform/main.tf 

If I were to continue this exercise to deploy in different environments, all the environments could be deployed in one script with different parameters for each respective environment to fulfill what is necessary, such as sizing, scaling, etc. 

Then the application would have to be deployed to the respective environments.

Implementing the Application Load Balancer Ingress Controller would also be necessary to route traffic seamlessly between the environments. 

## Known Issues
1. In the terraform init, delete the upper bound of terraform version 
2. Before terraform apply, change all list("") to tolist([]) as the function is deprecated
3. I currently set manage_aws_auth as false in the variable files to continue the deployment while bypassing some security measures for this exercise due to some errors 



## Author
Jiham Lee