# Terraform S3 Bucket Management

This Terraform configuration manages your AWS S3 bucket.

## Prerequisites

1. **Terraform installed** - Download from https://www.terraform.io/downloads.html
2. **AWS CLI configured** - Run `aws configure` and provide your AWS credentials
3. **AWS credentials** - Ensure you have IAM permissions for S3 operations

Verify setup:
```bash
terraform --version
aws s3 ls
```

## Setup Steps

### Step 1: Initialize Terraform

```bash
cd terraform
terraform init
```

This command:
- Downloads the AWS provider
- Creates the `.terraform` folder
- Initializes the local state file

### Step 2: Import Your Existing S3 Bucket

Since you created the bucket manually in AWS Console, you need to import it into Terraform state:

```bash
terraform import aws_s3_bucket.mlops_bucket mlops-bucket-created-for-dvc
```

Expected output:
```
aws_s3_bucket.mlops_bucket: Importing from ID "mlops-bucket-created-for-dvc"...
aws_s3_bucket.mlops_bucket: Import successful!
```

### Step 3: Verify the Import

```bash
terraform state show aws_s3_bucket.mlops_bucket
terraform plan
```

**Important**: The `terraform plan` should show no changes if everything is correct.

### Step 4: (Optional) Create terraform.tfvars

Create `terraform.tfvars` for custom values:

```hcl
aws_region      = "ap-south-1"
bucket_name     = "mlops-bucket-created-for-dvc"
environment     = "dev"
project_name    = "MLOps"
enable_versioning = true
```

### Step 5: Apply Configuration

```bash
terraform apply
```

## File Structure

```
terraform/
├── main.tf           # Main S3 bucket configuration
├── variables.tf      # Variable definitions
├── outputs.tf        # Output values
├── terraform.tfvars  # Variable values (create if needed)
└── .gitignore        # Files to exclude from git
```

## Common Commands

```bash
# Plan changes
terraform plan

# Apply changes
terraform apply

# Destroy resources (use with caution!)
terraform destroy

# View current state
terraform state show aws_s3_bucket.mlops_bucket
terraform state list

# Format code
terraform fmt
```

## Troubleshooting

### Error: "Error reading S3 Bucket"
- Ensure bucket name is correct in the terraform import command
- Verify AWS credentials are configured

### Error: "Access Denied"
- Check IAM permissions (need s3:GetBucket, s3:ListBucket, etc.)

### State already exists
- If you get "resource already exists" during apply after import, the bucket is already in state - this is normal!

## Safety Notes

1. **Backup your existing configuration** before applying changes
2. **Use `terraform plan`** before `terraform apply` to preview changes
3. **Never push `terraform.tfvars`** to git (contains sensitive values)
4. **Review .gitignore** to protect state files containing AWS information

## Next Steps

- Add more S3 configurations (lifecycle rules, cors, logging, etc.) in `main.tf`
- Use variables for different environments
- Consider Remote State for team collaboration (S3 + DynamoDB lock)
