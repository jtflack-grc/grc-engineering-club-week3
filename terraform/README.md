# Week 1 starter: Your First Compliant Resource
cat > README.md <<'EOF'
# GRC Engineering Club Challenge - Week 1

## Your First Compliant Resource

This project implements a Terraform-managed AWS S3 storage baseline that maps infrastructure configuration directly to NIST SP 800-53 control intent.

## Controls Implemented

| Control | Implementation |
|---|---|
| SC-28 | Server-side encryption using AES256 on primary and log buckets |
| AC-3 | Public access blocked across all four S3 public access vectors |
| CM-6 | Versioning enabled on the primary bucket |
| CM-6 | Required default tags applied through the AWS provider |
| AU-3 / AU-6 | Primary bucket access logging delivered to a dedicated log bucket |

## Evidence

Machine-readable evidence is generated from the Terraform plan:

```bash
terraform plan -out=tfplan
terraform show -json tfplan > evidence/plan.json
