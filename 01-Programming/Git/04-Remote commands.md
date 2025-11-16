# Commands related to remote interaction

### Adding a remote origin
```
git remote add origin <Remote_origin_URL>
```

### Updating the view of the remote repository
```
git fetch
```
This command contacts the remote repository (usually origin) and downloads any new data, like commits, branches, or tags, without changing your working directory or current branch.


### Pushing current changes to the remote origin
This variant of the command is used to push a **NEW** local branch to the remote repository and set the newly pushed remote branch as the "Upstream" of the local branch.
```
git push -u origin <Branch_name>
```
This is only used to push a local branch that doesn't exist in the remote repository. To move to a branch that already exists in the remote origin, check proper checkout methods down below.  

If a local branch already has an upstream branch then the push command can be used like this:
```
git push
```

### The concept of "Upstream" and "Tracking"
Local branches can have an Upstream branch in the remote repository. The Upstream branch is basically where the local branch "Tracks" changes. The local branch is connected to the Upstream which means that pushes and pulls can be done without having to specify an origin every time.

#### Command to check the branch tracking information
```
git branch -vv
```

### Setting the upstream branch without pushing
```
git branch --set-upstream-to=origin/<remote_branch>
```

### Cloning a remote repository
To clone a remote repository, use the following command
```
git clone <Repository_URL>
``` 

#### How cloning works
When you clone a remote repository, the data from the entire repository is downloaded to your PC, this means that every remote branch's data exists in your storage. With that said, the only branch that will automatically be created locally is the Default branch (usually "main" or "master"). In other words, only the default branch of the repository will be automatically checked out. All other branches will appear as remote-tracking branches (e.g., `origin/dev`) but **won’t have local counterparts** until you explicitly check them out.


### How to check out properly to remote branches
If you're using Git 2.23 or newer you can use:
```
git switch <Branch_name>
```
or
```
git checkout <Branch_name>
```
#### How this command works
1. Git checks if a branch named `<Branch_name>` exists locally, if it exists then it moves to it.
2. If a branch named `<Branch_name>` doesn't exist locally, then it looks for a branch named `origin/<Branch_name>` in the remote origin
3. If it finds a branch like that in the remote origin, it creates a branch named `<Branch_name>` locally and sets `origin/<Branch_name>` as its upstream. Then it checks out `<Branch_name>`.
4. If neither branch exists then git throws an error message: ```error: pathspec 'feature-branch' did not match any file(s) known to git```


For older versions, the explicit method is used
```
git checkout -b <Branch name> origin/<Branch_to_track>
```

### Pulling latests changes of a branch
Explicit way:
```
git pull <remote-name> <branch-name>
```
If the branch already has an **Upstream** branch set, then:
```
git pull
```