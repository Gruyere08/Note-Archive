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
It’s useful to create a new branch from a specific point in the past. **This will create a detached HEAD.**

---

### The concept of HEAD:
The **HEAD** is a pointer that references the commit you’re currently viewing.  
It usually points to the latest commit in a specific branch, but using the `git checkout` command  
you can move the HEAD to a previous commit — this is known as a *“detached HEAD”* state.

### Git Index or Staging area
The Git Index, also known as Staging area, is an intermediate place between the working directory and the repository. It holds a precise snapshot of what you plan to commit yet. It’s not your working directory, and it’s not your repository history. It’s the liminal space between the two.

### Useful Summary:
* **Staged changes**: These are the changes that are ready to be committed. You can stage changes using `git add <file_name>`
* **Index (Staging Area)**: Snapshot of selected changes. What you plan to commit.
* **Working directory**: Your actual directory in disk that contains your files. Where you edit.
* **Repository (HEAD)**: Committed history. What’s already sealed.

## Different ways of rolling back code
### Going back to a previous commit while deleting commit history
To go back to a previous commit while undoing all the commits that happened since that previous commit, we use the command `git reset`. There are different variants of this command, with different behaviours. 
#### Soft reset
```
git reset --soft <commit_hash>
```
This type of reset, rolls back the repository to a previous commit, while keeping the changes staged. All the changes that were made following that commit will be kept in the working directory and will be staged.

#### Mixed reset (Default)
```
git reset <commit_hash>
```
or
```
git reset --mixed <commit_hash>
```
This type of reset, rolls back the repository to a previous commit, while keeping the changes only in the working directory. All the changes that were made following that commit will be kept in the working directory, so you can decide what to keep and stage them later.


#### Hard reset
```
git reset --hard <commit_hash>
```
This type of reset, rolls back the repository to a previous commit, while deleting all changes made after that commit. 

---

### Going back to a previous commit while keeping commit history


