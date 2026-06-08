# Contributing to GophKeeper

Thank you for your interest in contributing to our project! This document outlines the guidelines, branching strategies, and architectural patterns required to keep our codebase clean, maintainable, and secure.

## 🔏 Commit Signing (SSH-based)

To ensure the integrity of our codebase and prevent identity impersonation, **all developers must sign their Git commits**. We use native SSH signing, which is supported in Git 2.34+ and does not require setting up a separate GPG keyring.

### 0. Generate an SSH Key (If you don't have one)
If you do not have an SSH key pair yet, or want to create a dedicated, secure key for this project, generate an **Ed25519** key (which is highly recommended for security and speed) by running:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

### 1. Configure Git for SSH Signing
Run the following commands on your local machine to configure Git globally to use your existing SSH key (preferably `Ed25519`) for signing commits and tags:
```bash
# Tell Git to use SSH format for signing instead of GPG
git config --global gpg.format ssh

# Specify the path to your PUBLIC SSH key to act as your signing identity
git config --global user.signingkey ~/.ssh/id_ed25519.pub

# Enable mandatory signing for all future commits and tags
git config --global commit.gpgsign true
git config --global tag.gpgsign true
```

### 2. Register your Signing Key on Github Platform

You must tell the platform that your SSH key is authorized to sign your commits, otherwise your PR commits will be marked as "Unverified".

1. Go to **Settings** → **SSH and GPG keys**.
2. Click **New SSH key**.
3. Change the **Key type** dropdown from *Authentication Key* to **Signing Key**.
4. Paste the content of your public key (`~/.ssh/id_ed25519.pub`) and save.

### 3. Verification

Once configured, you can make a test commit. When you push to the remote repository, a green **Verified** badge should appear next to your commit in the Pull Request history.

If you ever need to bypass or verify a single commit manually, you can use:

```bash
git commit -S -m "feat: manual signed commit test"
```

---

## 🔄 Git Workflow: GitFlow Approach

We adhere to the standard **GitFlow** branching model. All development is done in isolated feature branches and integrated into the ecosystem through strict environment tracking.

### Core Branches:

* `main`: Reflects the production-ready state. Only stable, tested releases are merged here.
* `dev`: The main integration branch for development. All features are branched from and merged back into `dev`.

### Supporting Branches:

* `feature/*`: Used to develop new features. Branches off `dev`, merges back into `dev`.
* `hotfix/*`: Used for quick production fixes. Branches off `main`, merges into both `main` and `develop`.

### Steps to Implement a New Feature:

1. **Start with the latest development state:**
```bash
git switch dev
git pull origin dev
```

2. **Create your feature branch:**
Use a descriptive name under the `feature/` namespace:
```bash
git switch -c feature/implement-secret-repository
```

3. **Commit your changes:**
Write clean, meaningful commit messages using the imperative mood (e.g., `feat: add upsert method to SecretRepository using ON CONFLICT`).
4. **Push your branch to the remote repository:**
```bash
git push origin feature/implement-secret-repository
```

5. **Open a Pull Request (PR):**
Open a PR targets the **`dev`** branch (NOT `main`). At least one team member must review and approve the PR before merging.

---

## 🏗️ Architectural Guidelines

We manage both the **Backend API** and the **CLI Application** inside this single repository. To prevent tightly coupled code and maintain scalability, we enforce strict architectural boundaries for each component.

### 📁 Codebase Directory Structure
```text
.
├── backend             # Backend API Service
│   ├── api             # Presentation layer (HTTP server, Routers, DTOs)
│   ├── domain          # Pure business entities
│   ├── repositories    # Infrastructure layer
│   └── services        # Application business logic layer
└── cli                 # Client CLI Application
    ├── client          # HTTP API client layer
    ├── commands        # CLI command definitions and user interface
    ├── core            # Cryptographic core
    └── storage         # Local secret database handlers and schemas
```
### 🚫 1. Cross-Boundary Rule

Since this is a mono-repo, it is easy to accidentally cross-import modules. We enforce a strict boundary rule:

* **Code inside `cli/` must NEVER import anything from `backend/` and vice versa.**
* They are two entirely isolated software systems that happen to live in the same git repository. Their only contract of interaction is the **network API layer**.
