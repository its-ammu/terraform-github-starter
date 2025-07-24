# Terraform GitHub Actions Starter Template

A starter template for deploying infrastructure using Terraform with GitHub Actions. This repository provides a production-ready CI/CD pipeline for Terraform, including best practices for security, linting, and multi-environment deployments.

## Features

- **Automated Deployments**: Deploys to multiple environments (dev, qa, preprod, prod) on push to relevant branches.
- **Pull Request Checks**: Runs formatting, validation, linting, and security scans on every PR.
- **AWS OIDC Integration**: Uses GitHub OIDC for secure AWS authentication.
- **State Management**: Supports remote state with S3 and DynamoDB.
- **Security & Compliance**: Integrates TFLint and Checkov for code quality and security.
- **Approval Gates**: Manual approval required for preprod and prod deployments.

## Folder Structure

```
.
├── .github/
│   └── workflows/
│       ├── deploy.yaml         # Main deployment pipeline
│       └── pr-checks.yml       # PR checks for Terraform code
├── .checkov_skip_checks        # List of Checkov checks to skip
├── .tflint.hcl                 # TFLint configuration
├── README.md
├── main.tf                     # Example Terraform configuration
├── variables.tf                # Example variables definition
└── outputs.tf                  # Example outputs definition
```

## Getting Started

### Prerequisites

- AWS account with roles for each environment (dev, qa, preprod, prod)
- S3 bucket and DynamoDB table for Terraform state and locking
- GitHub repository secrets set for each environment's role ARN and state config

### Setup

1. **Fork or clone this repository.**
2. **Update the following:**
   - Secrets and variables in your GitHub repo (see below)
   - Backend configuration (S3 bucket, DynamoDB table names)
   - Environment-specific tfvars files (e.g., `environments/dev.tfvars`)
3. **Add your Terraform code** (e.g., `main.tf`, `variables.tf`, `outputs.tf`).

### GitHub Secrets & Variables

Set the following secrets in your repository for each environment:

- `DEV_ASSUME_ROLE_ARN`, `QA_ASSUME_ROLE_ARN`, `PREPROD_ASSUME_ROLE_ARN`, `PROD_ASSUME_ROLE_ARN`
- `AWS_DEV_STATE_BUCKET`, `AWS_QA_STATE_BUCKET`, etc.
- `AWS_DEV_LOCK_TABLE`, `AWS_QA_LOCK_TABLE`, etc.
- `WORKSPACE_KEY_PREFIX`

## Badges

You can add the following badges to your README for visibility:

| Badge | URL | Description |
|-------|-----|-------------|
| ![CI](https://github.com/its-ammu/terraform-github-starter/actions/workflows/pr-checks.yml/badge.svg) | https://github.com/OWNER/REPO/actions/workflows/pr-checks.yml/badge.svg | PR Checks Status |
| ![Deploy](https://github.com/its-ammu/terraform-github-starter/actions/workflows/deploy.yaml/badge.svg) | https://github.com/OWNER/REPO/actions/workflows/deploy.yaml/badge.svg | Deployment Pipeline Status |

Replace `OWNER/REPO` with your GitHub username and repository name.

## Reporting & Artifacts

- **Terraform Plan**: The PR workflow uploads the Terraform plan as an artifact for review.
- **tfsec & Checkov Reports**: Linting and security scan results are uploaded as SARIF reports and integrated into the GitHub Security tab for inline review.

### Workflows

#### 1. PR Checks (`.github/workflows/pr-checks.yml`)

- Runs on pull requests to `develop` and `main`
- Steps:
  - Terraform format check
  - Terraform init & validate
  - Terraform plan (plan file uploaded as artifact)
  - TFLint linting (SARIF report uploaded)
  - TFSec security scan (SARIF report uploaded, bot will be adding PR comments) 
  - Checkov security scan (SARIF report uploaded, configurable via `.checkov_skip_checks`)

*Note : You can use checkov or tfsec based on your needs and remove the other one.*

#### 2. Deployment Pipeline (`.github/workflows/deploy.yaml`)

- Runs on push to `develop` (dev) and `main` (qa, preprod, prod)
- Steps:
  - Checkout & setup Terraform
  - Configure AWS credentials via OIDC
  - Terraform init with remote backend
  - Workspace selection/creation
  - Terraform plan & apply
  - Manual approval for preprod/prod

## Example Usage

1. Open a PR to `develop` or `main` — PR checks will run automatically.
2. Merge to `develop` — triggers deployment to dev.
3. Merge to `main` — triggers deployment to qa, then preprod (with approval), then prod (with approval).

## Customization

- Add or modify environments in `deploy.yaml`.
- Adjust PR checks as needed in `pr-checks.yml`.
- Update `.checkov_skip_checks` and `.tflint.hcl` for your policies.

## License

[MIT](LICENSE)
