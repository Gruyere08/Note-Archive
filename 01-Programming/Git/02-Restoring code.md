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
