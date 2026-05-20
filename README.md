# Terraform + GitHub Actions — AWS EC2 Provisioning

![Terraform](https://img.shields.io/badge/Terraform-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)
![HCL](https://img.shields.io/badge/HCL-100%25-7B42BC?style=for-the-badge)

---

## 📌 Project Overview

This project demonstrates how to automate **AWS EC2 instance provisioning** using **Terraform** as Infrastructure as Code, triggered automatically via a **GitHub Actions CI/CD pipeline**. Every push to the `main` branch runs the Terraform workflow — initializing, planning, and applying infrastructure changes without any manual intervention.

| Component | Details |
|---|---|
| Cloud Provider | AWS (`ap-south-1`) |
| Resource | EC2 Instance (`t2.micro`) |
| AMI | `ami-0f58b397bc5c1f2e8` |
| Instance Name | `GitHubActionsTerraform` |
| IaC Tool | Terraform (AWS Provider `v6.43.0`) |
| CI/CD | GitHub Actions (`.github/workflows/`) |
| Language | HCL 100% |

---

## 🏗️ Architecture

```
  Developer Push to main
          │
          ▼
┌─────────────────────────────────┐
│       GitHub Actions CI/CD      │
│                                 │
│  1. terraform init              │
│  2. terraform plan              │
│  3. terraform apply --auto-     │
│     approve                     │
└──────────────┬──────────────────┘
               │  AWS credentials
               │  via GitHub Secrets
               ▼
┌─────────────────────────────────┐
│         AWS (ap-south-1)        │
│                                 │
│   aws_instance.test_server      │
│   ├── AMI:  ami-0f58b397bc5c.. │
│   ├── Type: t2.micro            │
│   └── Tag:  GitHubActionsTerra- │
│             form                │
└─────────────────────────────────┘
```

---

## ✨ Features

- **Fully automated provisioning** — EC2 instance created on every push via GitHub Actions, no manual `terraform apply` needed
- **Minimal, clean Terraform config** — just `provider.tf` and `main.tf`, easy to understand and extend
- **Pinned provider version** — AWS provider locked to `v6.43.0` via `.terraform.lock.hcl` for reproducible runs
- **Secure credential handling** — AWS credentials passed via GitHub Actions Secrets (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`), never hardcoded
- **Screenshots included** — pipeline output, terminal output, and EC2 provisioning result documented in the repo

---

## 📁 Repository Structure

```
terraform-githubactions-ec2/
├── .github/
│   └── workflows/
│       └── terraform.yml          # GitHub Actions pipeline
├── main.tf                        # EC2 instance resource definition
├── provider.tf                    # AWS provider config (ap-south-1)
├── .terraform.lock.hcl            # Provider lock file (AWS v6.43.0)
├── .gitignore
├── terraform-github-action-output.png    # EC2 provisioning output
├── terraform-github-action-pipeline.png  # GitHub Actions pipeline view
└── terraform-github-action-terminal.png  # Terminal output screenshot
```

---

## 🚀 Getting Started

### Prerequisites

- [Terraform](https://developer.hashicorp.com/terraform/install) >= 1.0
- AWS account with EC2 permissions
- GitHub repository with Actions enabled

### 1. Clone the Repository

```bash
git clone https://github.com/Petchimuthu19/terraform-githubactions-ec2.git
cd terraform-githubactions-ec2
```

### 2. Add GitHub Secrets

Go to your repository → **Settings → Secrets and variables → Actions** and add:

| Secret Name | Description |
|---|---|
| `AWS_ACCESS_KEY_ID` | Your AWS IAM access key |
| `AWS_SECRET_ACCESS_KEY` | Your AWS IAM secret key |

> ⚠️ Never commit AWS credentials to the repository. Always use GitHub Secrets.

### 3. Push to `main`

```bash
git add .
git commit -m "Trigger Terraform deployment"
git push origin main
```

GitHub Actions will automatically run:
```
terraform init → terraform plan → terraform apply
```

### 4. Run Locally (Optional)

```bash
# Configure AWS credentials locally
aws configure

# Initialize Terraform
terraform init

# Preview the plan
terraform plan

# Apply the infrastructure
terraform apply

# Destroy when done
terraform destroy
```

---

## 📄 Terraform Resource Reference

### `provider.tf`

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

### `main.tf`

```hcl
resource "aws_instance" "test_server" {
  ami           = "ami-0f58b397bc5c1f2e8"
  instance_type = "t2.micro"

  tags = {
    Name = "GitHubActionsTerraform"
  }
}
```

### `.terraform.lock.hcl`

Provider pinned to:

```
provider "registry.terraform.io/hashicorp/aws" {
  version = "6.43.0"
}
```

---

## 🔁 GitHub Actions Workflow

The workflow file at `.github/workflows/terraform.yml` triggers on every push to `main` and runs the full Terraform lifecycle:

```yaml
on:
  push:
    branches:
      - main

jobs:
  terraform:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v2

      - name: Terraform Init
        run: terraform init
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}

      - name: Terraform Plan
        run: terraform plan
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}

      - name: Terraform Apply
        run: terraform apply --auto-approve
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```

---

## 📸 Output Screenshots

| File | Description |
|---|---|
| `terraform-github-action-pipeline.png` | GitHub Actions pipeline — all steps green |
| `terraform-github-action-output.png` | EC2 instance successfully provisioned on AWS |
| `terraform-github-action-terminal.png` | Terminal output of Terraform apply |

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| **Terraform** | Infrastructure as Code — defines and provisions the EC2 instance |
| **AWS EC2** | Virtual machine provisioned in `ap-south-1` |
| **AWS Provider v6.43.0** | Terraform provider for AWS resource management |
| **GitHub Actions** | CI/CD pipeline that triggers `terraform apply` on push |
| **GitHub Secrets** | Secure storage for AWS credentials |

---

## ⚠️ Important Notes

- This project provisions a **real AWS EC2 instance** that may incur charges. Run `terraform destroy` when done to avoid unexpected costs.
- The instance type is `t2.micro`, which is eligible for the **AWS Free Tier** for the first 12 months.
- The AMI `ami-0f58b397bc5c1f2e8` is region-specific to `ap-south-1` (Mumbai). Update it if deploying to a different region.

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Commit your changes (`git commit -m 'Add your feature'`)
4. Push to the branch (`git push origin feature/your-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

> Automating cloud infrastructure with code — every push provisions, every change is tracked.
