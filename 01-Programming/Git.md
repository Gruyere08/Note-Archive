# Git Notes

## Basic commands

*01 July 2025 — 15:48*

---

### Creating a new Repo:
```console
git init
```

---

### Changing the initial name of repositories:
```console
git config --global init.defaultBranch <Branch_Name>
```

It is recommended to configure the initial branch name as `main` using this command:
```console
git config --global init.defaultBranch main
```

---

### Show useful information about the repo:
Displays untracked files, current branch, and past commits:
```console
git status
```

---

### Add files to tracking:
```console
git add <file_directory>
```

Add **all files** to tracking:
```console
git add .
```

---

### Create a commit with a message:
```console
git commit -m 'Message'
```

---

### View history of commits:
```console
git log
```

---

# Restoring code

*01 July 2025 — 16:13*

---

### Using `git checkout`:
Using the `git log` command, you can check the history of commits.  
If you copy one of the commit hashes, you can view the project in the state of that commit:

```console
git checkout <Hash-code>
```

This command moves the HEAD to that previous commit without deleting the next ones.  
It’s useful to create a new branch from a specific point in the past.

---

### The concept of HEAD:
The **HEAD** is a pointer that references the commit you’re currently viewing.  
It usually points to the latest commit in a specific branch, but using the `git checkout` command  
you can move the HEAD to a previous commit — this is known as a *“detached HEAD”* state.

---

# Branch related commands

*01 July 2025 — 16:39*

---

### Move to a branch:
```console
git checkout <branch_name>
```

---

### Discard changes made in a detached HEAD and move to a branch:
```console
git checkout -f <branch_name>
```

