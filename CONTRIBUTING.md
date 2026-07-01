# Contributing to Proton Pass Secret Fetcher Action 🚀

First off, thank you for taking the time to contribute! Contributions from the community make tools like this safer, faster, and more reliable for everyone.

By participating in this project, you agree to abide by standard open-source etiquette and maintain secure coding practices.

---

## 🛣️ Code Contribution Workflow

Please follow these structured steps to propose changes to the action engine:

1. **Fork the Repository**: Create a personal copy of this repository on your GitHub account.
2. **Clone and Branch**: Clone your fork locally and create a tightly scoped feature branch:
   ```bash
   git checkout -b feat/your-descriptive-feature-name
   ```
3. **Implement Changes**: Update the `action.yml` file or project assets. Ensure your shell commands align with the strict POSIX guidelines below.
4. **Document Updates**: If your code adds new configurations, input blocks, or capabilities, update the `README.md` to reflect them.
5. **Commit Code**: Write atomic, descriptive commit messages:
   ```bash
   git commit -m "feat: implement dynamic field fallback mapping"
   ```
6. **Push and Pull Request**: Push the branch to your fork and submit a clean **Pull Request (PR)** targeting our `main` branch.

---

## 🛠️ Development & Coding Standards

To maintain standard portability across all default GitHub runner architectures (Ubuntu, macOS, etc.), all modifications must meet these operational criteria:

- **Strict Error Trapping**: Keep `set -eo pipefail` active at the top of the shell runner runtime block. Commands must terminate explicitly upon failure.
- **No Side-Effects**: This action must remain completely headless. Do not write actions that drop active user sessions, generate interactive login prompts, or write unmasked persistence caches to the runner disk.
- **Variable Masking**: Any script modification that touches raw secret data **must** mask every emitted line so multiline payloads (for example SSH keys) are not printed in logs:
  ```bash
  while IFS= read -r secret_line || [[ -n "$secret_line" ]]; do
    if [ -n "$secret_line" ]; then
      echo "::add-mask::$secret_line"
    fi
  done <<< "$FETCHED_SECRET"
  ```
- **Line Wrapping**: Always structure environment variable injections with a unique per-secret heredoc delimiter when writing to `GITHUB_ENV` so embedded lines cannot accidentally terminate the block.

---

## ✅ Automated Testing on `main`

This repository runs an automated integration test workflow on every push to `main` (including post-merge commits). You can also run it manually via **workflow_dispatch**.

Current integration test convention:

```bash
TEST_SECRET=pass://TestVault/TEST/TEST_SECRET
```

The CI job executes the local composite action and verifies that `TEST_SECRET` is injected into `GITHUB_ENV` with the expected value from Proton Pass.

Required repository secret:

```text
PROTON_PAT
```

---

## 🐛 Reporting Bugs & Feature Requests

### Found a Bug?

- Open a formal **GitHub Issue**.
- Include the exact runner operating system matrix (e.g., `ubuntu-latest`, `ubuntu-22.04`).
- Provide a sanitized snippet of your workflow `.yml` setup alongside the specific non-sensitive pipeline log stack trace output.

### Want a Feature?

- Search existing issues first to avoid duplicate proposals.
- Open an issue explaining **why** the feature is necessary, providing a concrete example use-case scenario.

---

## 🚨 Critical Security Vulnerabilities

> [!IMPORTANT]
> **Do NOT open public GitHub Issues for security vulnerabilities.**

If you discover a potential log leakage vulnerability, encryption weakness, or token sanitization bypass bug, please report it privately:

1. Navigate to the **Security** tab at the top of this repository.
2. Click **Vulnerability reporting** on the left menu.
3. Select **Report a vulnerability** to privately disclose the issue directly to the maintainer.

---

## 📄 Developer Certificate of Origin (DCO)

By contributing to this repository, you certify that your contributions are your original creation or that you have the legal right to submit them under the project's **MIT License**.
