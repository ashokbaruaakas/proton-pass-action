# Proton Pass Secret Fetcher Action 🔒

A high-performance, secure GitHub Composite Action to install the Proton Pass CLI and inject multiple secrets directly into your workflow's `GITHUB_ENV` context in a single execution step.

---

## 🚀 Quick Start Example

Add this step to your application workflow file (e.g., `.github/workflows/ci.yml`).

```yaml
name: Production Deployment

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      # 1. Fetch all required passwords/tokens from Proton Pass in one go
      - name: Fetch Environment Secrets
        uses: ashokbaruaakas/proton-pass-action@v0
        with:
          proton_pat: ${{ secrets.PROTON_PAT }}
          secrets_map: |
            GHCR_TOKEN=pass://MyVault/GHCR/GHCR_TOKEN
            DB_PASSWORD=pass://MyVault/Production/DB_PASSWORD
            STRIPE_KEY=pass://MyVault/Billing/STRIPE_API_KEY

      # 2. Access variables natively via standard shell environment syntax
      - name: Log in to GitHub Container Registry
        run: |
          echo "${GHCR_TOKEN}" | docker login ghcr.io -u ${{ github.actor }} --password-stdin

      - name: Run Database Migrations
        run: |
          ./run-migrations.sh --password "${DB_PASSWORD}"
```

---

## 🛠️ Action Inputs

| Input         | Description                                                                                          | Required | Default |
| :------------ | :--------------------------------------------------------------------------------------------------- | :------: | :-----: |
| `proton_pat`  | Your Proton Pass Personal Access Token. Securely store this in your repository's Actions Secrets.    | **Yes**  | _None_  |
| `secrets_map` | A newline-separated list using the exact format `ENVIRONMENT_VARIABLE_NAME=pass://vault/item/field`. | **Yes**  | _None_  |

---

## 🔒 Security Best Practices

1. **Log Masking**: This action automatically applies GitHub's `::add-mask::` syntax to all fetched values. If a following step accidentally attempts to print or echo a loaded variable, it will display safely in the runner console as `***`.
2. **Token Scope**: Ensure your Personal Access Token has been granted explicit permissions on your local machine before executing the pipeline:
   ```bash
   pass-cli pat access grant --pat-name "Your-Token-Name" --vault-name "MyVault" --role viewer
   ```
3. **No Local Profiles**: This action communicates statefully using the official `--no-session` headless workflow. It leaves behind no active login states or authorization caches on the host GitHub runner filesystem.

---

## 📝 Parsing Syntax Reference

The `secrets_map` parameter reads standard Proton Pass entry schemas:

- **Password**: `pass://VaultName/ItemName/password`
- **Username**: `pass://VaultName/ItemName/username`
- **2FA Code (TOTP)**: `pass://VaultName/ItemName/totp`
- **Custom Field**: `pass://VaultName/ItemName/YourCustomFieldName`

---

## 🤝 Contributing

Contributions are what make the open-source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

Please see our [CONTRIBUTING.md](CONTRIBUTING.md) guide for details on our code standards, how to submit feature requests, and how to privately report security vulnerabilities.

---

## 📄 License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for the full text. Feel free to use, modify, and distribute it in both personal and commercial projects.
