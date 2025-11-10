# Git Notes

## Basic commands

*01 July 2025 — 15:48*

---

### Creating a new Repo:
```console
git init
```



### Changing the initial name of repositories:
```console
git config --global init.defaultBranch <Branch_Name>
```

It is recommended to configure the initial branch name as `main` using this command:

```console
git config --global init.defaultBranch main
```



### Show useful information about the repo:
Displays untracked files, current branch, and past commits:
```console
git status
```



### Add files to tracking:
```console
git add <file_directory>
```

Add **all files** to tracking:
```console
git add .
```



### Create a commit with a message:
```console
git commit -m 'Message'
```



### View history of commits:
```console
git log
```


