# 📚 Course Contents

1. Introduction to Git & GitHub
2. Download Git & Create GitHub Account
3. Install GitHub Desktop
4. Basic Git Commands
5. Create Repository (Remote & Local)
6. Push Code to Repository
7. Use Git in VS Code, GitHub Desktop & Visual Studio
8. Create Projects Using Git & GitHub
9. Hosting Static Website on GitHub (Optional)

---

# 1️⃣ Introduction to Git & GitHub

Git and GitHub are essential tools for **version control** and **collaboration** in modern software development.

## 🔹 What is Git?

Git is a **distributed version control system** that:

- Tracks changes in source code
- Saves snapshots (commits)
- Allows branching & merging
- Works locally

Think of Git as a **time machine for your code**.

### Example

If you break your website layout, you can revert to a previous version instantly.

---

## 🔹 What is GitHub?

GitHub is a **cloud platform that hosts Git repositories**.

It allows teams to:

- Collaborate
- Create Pull Requests
- Track Issues
- Run CI/CD
- Review code online

---

## 🔎 Key Differences

| Feature   | Git                    | GitHub           |
| --------- | ---------------------- | ---------------- |
| Type      | Version Control System | Hosting Platform |
| Ownership | Open Source Community  | Microsoft        |
| Works     | Local machine          | Web-based        |
| Interface | Command Line           | Web UI           |

---

# 2️⃣ Download Git & Create GitHub Account

## 🔹 Install Git

Download:
[https://git-scm.com/](https://git-scm.com/)

Verify installation:

```bash
git --version
```

---

## 🔹 Create GitHub Account

Sign up at:
[https://github.com/](https://github.com/)

---

## 🔹 Configure Git

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --list
```

This ensures your commits are properly identified.

---

# 3️⃣ Install GitHub Desktop

Download from:
[https://desktop.github.com/](https://desktop.github.com/)

GitHub Desktop provides a graphical interface for Git operations.

Ideal for beginners who prefer not to use command line.

---

# 4️⃣ Basic Git Commands

## 🔹 Branching

```bash
git checkout -b feature/new-login
```

Create and switch to a new branch.

```bash
git checkout main
```

Switch branch.

```bash
git branch
```

List branches.

---

## 🔹 Merge

```bash
git merge feature/new-login
```

Merge branch into current branch.

---

## 🔹 Delete Branch

```bash
git branch -d feature/new-login
git push origin --delete feature/new-login
```

---

## 🔹 View History

```bash
git log
```

---

## 🔹 Rollback

```bash
git checkout <commit_id>
git revert <commit_id>
```

---

## 🔹 Sync with Remote

```bash
git fetch
git pull
git push
```

---

# 5️⃣ Create Repository (Remote & Local)

## 🔹 Remote Repository (GitHub)

- Click **New Repository**
- Choose Public / Private
- Add README (optional)

---

## 🔹 Local Repository

```bash
git init
git remote add origin <repo_url>
```

---

# 6️⃣ Push Code (Local → Remote)

## 🧪 Demo Project

```bash
mkdir DemoProject
cd DemoProject
git init
echo "# KH Git Course" > README.md
git add .
git commit -m "Initial commit"
git remote add origin <REMOTE_URL>
git push -u origin main
```

---

# 7️⃣ Clone Repository (Remote → Local)

```bash
git clone <repo_url>
cd DemoProject
git status
```

---

# 8️⃣ Use Git in IDEs

## 🔹 VS Code

- Built-in Source Control panel
- Stage, commit, push visually
- Switch branches from bottom bar

## 🔹 GitHub Desktop

- Clone repositories
- View diffs visually
- Commit & push with buttons

## 🔹 Visual Studio

- Integrated Git support
- Manage branches visually
- Use terminal or UI

---

# 9️⃣ Create Projects with Git

Best Practices:

- Use branches for features
- Commit frequently
- Write clear commit messages
- Push regularly
- Use Pull Requests for collaboration

---

# 🌐 Hosting Static Website on GitHub Pages

## Steps

1. Create repository:
   `username.github.io`

2. Add `index.html`

3. Enable **Pages** in Settings
   Source: `main` branch

4. Access website:

```
https://username.github.io
```

Perfect for:

- Portfolio
- Documentation
- Simple static websites

---

# 🧪 Practice Project Idea

Create a simple:

- Portfolio website
- Landing page
- Documentation site

Manage it using Git branches and GitHub pull requests.

---

# ❓ Q & A

Ask questions about:

- Branching strategies
- Merge conflicts
- Pull Requests
- Git workflows
- Collaboration
- Git in real projects

---
