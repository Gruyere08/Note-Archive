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

### Rename a branch (Forcibly)
This command renames the branch forcibly, which means it replaces every branch named like `<branch_new_name>` if necessary.
```console
git checkout -M <branch_new_name>
```