In my solution, I deployed a Docker container running the Apache web server/httpd on a CentOS base image. The AWS cloud platform was chosen for provisioning resources,
utilising Terraform for Infrastructure as Code. I specifically created a VPC and an EKS resource group module. Helm charts were employed for Kubernetes deployments, and ArgoCD was used for GitOps.

- -
Available Options
Cloud Platform: AWS, GCP, Azure, etc.
IaC Tools: Terraform, AWS CloudFormation, Ansible, etc.
Containerization Tools: Docker, Kubernetes, etc.
GitOps Tools: ArgoCD, Flux, etc.
:
The Reasons Behind this Decisions:
AWS: It's a mature cloud platform with a comprehensive set of services, making it an ideal choice for this project.
Terraform: Enables version-controlling infrastructure and provides a simple way to create and manage resources on AWS.
Docker and CentOS: Docker for containerization simplifies deployment, and CentOS offers a stable and secure environment for the Apache web server.
Helm charts: Simplifies Kubernetes deployments and application management.
ArgoCD: Ideal for GitOps and ensures that the cluster state matches the configuration in the Git repository.
-
-
Enhacements:
High Availability: Deploy the application across multiple availabilityzones.
Security: Introduce a Web Application Firewall (WAF) and implement SSL/TLS for the Apache server.
Monitoring and Logging: Using tools like Prometheus and Grafana or AWS Opensearch service to add comprehensive monitoring and log.
Secret Management: Use AWS Secrets Manager or a similar service for better secret management.
Optimize Costs: Utilize AWS Spot Instances for non-critical, fault-tolerant components of the application.


### Run Terraform to create EKS Cluster

Go to terraform/resources/ folder and run 

Terraform Plan
```
terraform init -var-file="dev.tfvars"
terraform plan -var-file="dev.tfvars"
```

Terraform Apply
```
terraform init -var-file="dev.tfvars"
terraform apply -var-file="dev.tfvars"
```

### Build and Push the docker image to DockerHub

```
docker build -t vijayalakshman/httpd_webserver:latest .
docker push -t vijayalakshman/httpd_webserver:latest
```
### Install ArgoCD 

```
aws eks update-kubeconfig --region us-east-1 --name web-dev
kubectl create ns argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.5.8/manifests/install.yaml
```

Update service type from ClusterIP to LoadBalancer
```
kubectl get service argocd-server -n argocd -o json | jq '.spec.type = "LoadBalancer"' | kubectl apply -f -
# for windows os
kubectl get service argocd-server -n argocd -o json | jq ".spec.type = \"LoadBalancer\"" | kubectl apply -f -
```
### Fetch ArgoCD login details
Admin is the username and get password as

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | ForEach-Object { [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($_)) }; echo
```

### Access the ArgoCD server using Service External IP

```
kubectl get svc -n argocd
https://<<External DNS of argocd server>>/
```

### Run Argocd application

Create docker secret first 

```
kubectl create ns web-app
kubectl -n web-app create secret docker-registry docker-login --docker-server=https://index.docker.io/v1/ --docker-username= --docker-password= --docker-email=
kubectl apply -f web-app.yaml
```

### Access web application 

```
kubectl get svc -n web-app
http://<load-balancer>/index.html
```
