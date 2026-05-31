# Terraform Interview Cheatsheet

## Essential Commands

```bash
terraform init -backend-config="bucket=tf-state" -reconfigure
terraform plan -var-file="prod.tfvars" -out=tfplan
terraform apply tfplan
terraform destroy -target=module.eks
terraform state list
terraform state show aws_eks_cluster.this
terraform import aws_s3_bucket.this my-bucket-name
terraform taint aws_instance.web[0]   # force replacement
terraform output -json | jq .
```

## Common Interview Questions

**Q: Explain remote state and state locking**
Remote state stores tfstate in S3 (shared across team). State locking uses DynamoDB to prevent concurrent applies that cause corruption. Always use both in production.

**Q: What's the difference between count and for_each?**
- `count`: integer, creates N identical resources — harder to remove individual items without drift
- `for_each`: map/set, creates resources keyed by value — removing one item doesn't affect others
- Rule: use `for_each` for anything that may scale or need individual management

**Q: How do you handle secrets in Terraform?**
Never store secrets in tfstate or vars. Use:
1. AWS Secrets Manager / SSM Parameter Store data sources
2. HashiCorp Vault provider
3. `sensitive = true` on variables (masks in output, still in state)
4. Remote state encryption (S3 SSE-KMS)

**Q: What is `terraform refresh` and when should you use it?**
Updates local state to match real infrastructure without making changes. Use after manual AWS console changes or to detect drift. In modern TF, `plan -refresh-only` is preferred.

**Q: Explain Terraform module best practices**
- Version pin modules: `version = "~> 3.0"`
- Use semantic versioning
- One module = one logical component (vpc, eks, rds)
- Expose only necessary outputs
- Use `variable` validation blocks
- Keep modules DRY but not over-abstracted
