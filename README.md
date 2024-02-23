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

