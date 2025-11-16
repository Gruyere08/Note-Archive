# Branch related commands

*01 July 2025 — 16:39*

---

### Create a new branch:
```console
git branch <branch_name>
```
**The newly created branch will inherit the code of the branch you're currently on at the moment of creating the new branch**

### Explicit version of the last command, to create branches based on a specific branch:
```console
git branch <branch_name> <source_branch>
```

### Move to a branch:
```console
git checkout <branch_name>
```

### Create a branch and move to it:
```console
git checkout -b <branch_name>
```
or
```console
git switch -c <branch_name>
```

---

### Discard changes made in a detached HEAD and move to a branch:
```console
git checkout -f <branch_name>
```

### Rename a branch (Forcibly)
This command renames the branch forcibly, which means it replaces every branch named like `<branch_new_name>` if necessary.
```console
git checkout -M <branch_new_name>
```