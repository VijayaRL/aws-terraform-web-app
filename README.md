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
