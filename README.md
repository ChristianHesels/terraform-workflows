# Terraform Workflows für GitHub Actions

Dieses Repository enthält wiederverwendbare GitHub Actions Workflows zur Ausführung von Terraform-Operationen (Plan & Apply) in anderen Repositories. Ziel ist es, ein zentrales und wartbares Setup bereitzustellen, das in mehreren Infrastruktur-Repositories genutzt werden kann.

## Features

- Ausführung von `terraform plan` bei Pull Requests auf den `main`-Branch
- Ausführung von `terraform plan` **und** `terraform apply` bei Pushes auf den `main`-Branch
- Automatisches Setup von Terraform und AWS Credentials via GitHub OIDC
- Best Practices für sicheres und automatisiertes Deployment

## Verwendung in anderen Repositories

### Voraussetzungen

1. GitHub Actions müssen aktiviert sein.
2. Eine AWS IAM-Rolle muss existieren, die OIDC-Authentifizierung via GitHub Actions erlaubt.
3. Das Repository muss Zugriff auf dieses Repository (`terraform-workflows`) haben (entweder public oder via `workflow_call` Berechtigung bei privaten Repos).

### Beispiel für `main.yml` im Ziel-Repository

```yaml
name: Terraform Deployment

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  deploy:
    uses: ChristianHesels/terraform-workflows/.github/workflows/terraform.yml@main
    secrets:
      AWS_ROLE: ${{ secrets.AWS_ROLE }}
```
