# Terraform Workflows für GitHub Actions

Dieses Repository enthält wiederverwendbare GitHub Actions Workflows zur Ausführung von Terraform-Operationen (Plan, Apply und Destroy) in anderen Repositories. Ziel ist es, ein zentrales und wartbares Setup bereitzustellen, das in mehreren Infrastruktur-Repositories genutzt werden kann.

## Features

- **Deploy-Workflow**
  - `terraform plan` bei Pull Requests auf `main`
  - `terraform plan` **und** `terraform apply` bei Pushes auf `main`
- **Destroy-Workflow**
  - Manueller Trigger via `workflow_dispatch`
  - Führt `terraform destroy -auto-approve` aus
- Automatisches Setup von Terraform und AWS Credentials via GitHub OIDC
- Trennung von Deployment und Zerstörung für bessere Kontrolle
- Wiederverwendbar via `workflow_call`

---

## Voraussetzungen

- GitHub Actions ist im Ziel-Repository aktiviert
- Eine AWS IAM-Rolle erlaubt `AssumeRoleWithWebIdentity` via OIDC (z. B. `token.actions.githubusercontent.com`)
- Die Rolle erlaubt Zugriff für das jeweilige Repo (via `sub`-Claim in der IAM-Bedingung)
- Das Repository `terraform-workflows` muss öffentlich oder im selben GitHub Org sein (bei privaten Repos)

---

## Verwendung im Ziel-Repository

### ✅ Deployment

```yaml
name: Terraform Deployment

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  deploy:
    uses: ChristianHesels/terraform-workflows/.github/workflows/terraform-deploy.yml@main
    with:
      aws_role: arn:aws:iam::123456789:role/github-actions-role
      run_apply: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
    permissions:
      id-token: write
      contents: read
```

### 🧨 Zerstörung (manueller Trigger)

```yaml
name: Terraform Destroy

on:
  workflow_dispatch:

jobs:
  destroy:
    uses: ChristianHesels/terraform-workflows/.github/workflows/terraform-destroy.yml@main
    with:
      aws_role: arn:aws:iam::123456789:role/github-actions-role
    permissions:
      id-token: write
      contents: read
```
