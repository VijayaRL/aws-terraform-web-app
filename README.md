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
