---
title: 12-04-2025
author: Nishanth-2024
tags:
  - Git
  - Boot.dev
  - MyDevDen
---

## Git (Notes)

> [!Tip]
> Within this document `<...>` represent required arguments and `[...]` represents optional arguments for a command

### Commands

- High Level (porcelain)
  - `git status`
  - `git add`
  - `git commit`
  - `git push`
  - `git pull`
  - `git log`
- Low Level (plumbing)
  - `git apply`
  - `git commit-tree`
  - `git hash-object`

### Quick Config

- `git config --get user.name`
- `git config --get user.email`
- `git config --add --global user.name "github_username_here"`
- `git config --add --global user.email "email@example.com"`
- `git config init.defaultBranch master`

### Git Repository

- Chose a non version controlled folder ~/Documents/Dump
- `cd ~/Documents/Dump`
- `mkdir webflyx`
- `cd webflyx`
- `git init`
  - creates *.git* folder with following contents
    - HEAD
    - config
    - description
    - hooks
    - info
    - objects
    - refs

### Git Status

- `git status`
  - *untracked*: Not being tracked by Git
  - *staged*: Marked for inclusion in the next commit
  - *committed*: Saved to the repository's history

### Git add

- `git add <path to file | pattern>`

### Git commit

- `git commit -m 'commit message'`

### Git Log

- `git log`
  - interactive pager is enabled
    - press `q` to quit the pager
  - logs following for each commit
    - Who made a commit
    - When the commit was made
    - What was changed
- `git --no-pager log -n 10`
  - --no-pager: runs without interactive pager
  - -n: represents maximum number of commits shown

### Git Commit Hash

- unique because derived from content changes
- uses cryptographic SHA-1 to generate hash
  - The commit message
  - The author's name and email
  - The date and time
  - Parent (previous) commit hashes

### Plumbing

Git is made up of objects that are stored in the .git/objects directory. A commit is just a type of object.

- `git log -n 10` to find commit hash
- `ls -al .git/objects`
  - folder with first 2 letters of commit hash
  - folder contains actual file with commit details
  - use xxd to view the content but still has broken text

#### Contents of CommitMessage

- `git cat-file -p <commit-hash>` to view contents of commit.
- `git cat-file -p 87a8d8eee37aea292928058d8937b5064de0687b > /tmp/catfileout.txt` to save contents of commit to a file

```zsh
tree tree-hash
author <author details>
committer <committor details>
gpgsig <if any>
COMMIT_MESSAGE
```

#### Trees and Blobs

- `git cat-file -p <tree-hash>`
  - gives content of the blob that contains the actual commit changes
- `git cat-file -p <blob-hash>`
  - View actual commit contents

#### Optimisations

when a file has been added or changed there will be a hash associated with it

- `git cat-file -p <commit-hash>`
- `git cat-file -p <tree-hash>`
  - gives the file's blob hash
- After subsequent commit or commits not involving the changes in the file,
  - `git cat-file -p <new-commit-hash>`
  - `git cat-file -p <new-tree-hash>`
    - get file hash of the same file.
    - observe that it has not chnaged.

#### Git Config

- `git config --list --local`
- `git config --list --global`
- `cat .git/config`
- `git config --get <section>.<keyname>`
- `git config --unset <section>.<key>`
- `git config --unset-all <section>.<key>` to unset all instances including duplicates
  - useful if performed `git config --add ....` on same key multiple times
- `git config --remove-section section`

> [!NOTE]
> - git config keys are *case-insensitive*
> - `--local` is optional if `--global` is not specified it will default to `--local`

```zsh
git config --add --global user.name "User Name"
git config --add --global user.email "user-email-name@domain.com"

git config --add [--local] webflyx.ceo "ThePrimeagen"
git config --add [--local] webflyx.cto "TheLaneagen"
git config --add [--local] webflyx.valuation "mid"
```

There are several locations where Git can be configured. From more general to more specific, they are:

- *system*: /etc/gitconfig, a file that configures Git for all users on the system
- *global*: ~/.gitconfig, a file that configures Git for all projects of a user
- *local*: .git/config, a file that configures Git for a specific project
- *worktree*: .git/config.worktree, a file that configures Git for part of a project

### Branch

A branch is just a named pointer to a specific commit.

Rename branch
- `git branch -m oldname newname`

Create new Branch
- `git branch new_branch`
- `git switch -c new_branch`

Switching to new branch
- `git switch <branch name>` - recommended
- `git checkout <branchname>` 

### Git Log Flags

- `git log --decorate=<value>`
  - *no*: no branches
  - *short*(default): short branch name
  - *full*: full branch name including ref/...
- `git log --oneline`
- `git log --oneline --decorate=full`

### Git Files

- heads or tips of git branchs are stored in `.git/refs/heads`
- if we `cat` one of the files inside the above folder, we should be able to see commit hash that the branch the file points to
- `cat .git/refs/heads/<branch_name>`

### Git Log with Graph

- `git log --oneline --graph --all`
- `git log --graph`

### Git Merge a branch onto another

- `git merge <source_branch>` while on `destination branch`
- `git --no-pager log --oneline --decorate --graph --parents`
  - `*` represents commit in the repository

```zsh
 git log --oneline --decorate --graph --parents
Found existing alias for "git log --oneline --decorate --graph". You should use: "glog"
*   2c3f3c8 0ca89da 65529c2 (HEAD -> main) F: Merge branch 'add_classics' into 'main'
|\
| * 65529c2 8dc8624 (add_classics) D: add classics
* | 0ca89da 8dc8624 E: update contents.md
|/
* 8dc8624 493200b C: add quotes
* 493200b 87a8d8e B: add titles
* 87a8d8e A: add contents.md
```

### Delete a branch

- `git branch -d <branch-name>`

### Rebase

Rebase helps you to take commits from one branch and move them to tip of base branch

- create new branch from a particular commit `git switch -c <commit-hash>`
- to rebase current branch on-to main `git rebase main`
  - never rebase public branch on-to local branch
- Rebase avoids merge commits

### Git Reset

The `git reset` command can be used to undo the last commit(s) or any changes in
- the index (staged but not committed changes) and
- the worktree (unstaged and not committed changes).

#### reset --Soft

`git reset --soft COMMITHASH`

The `--soft` option is useful if you just want to go back to a previous commit, but keep all of your changes.
Committed changes will be uncommitted and staged, while uncommitted changes will remain staged or unstaged as before.

#### reset --Hard

`git reset --hard`
`git reset --hard <commit-hash>`

### git remote

`git remote add <name> <uri>` - This command adds remote url or relative path to local repo.

`git fetch` - This downloads copies of all the contents of the .git/objects directory
and other book-keeping information from the remote repository into your current one.

- This fetches the info from remote but not pull the content themselves

### Git Log Remote

`git log remote/branch`

### Git Merge Remote

`git merge remote/branch` - 

### Git Push / Pull

`git push origin <localbranch>:<remotebranch>`
`git push origin :<remotebranch>`
`git pull [<remote>/<branch>]`

> [!Tip]
> To keep linear history on git pull - `git config --global pull.rebase true`

### Git Ignore

.gitignore - holds the files or patterns to ignore while comitting to a repo

- `*` matches all characters except for `/`
  - `*.txt` ignores *anyfilename.txt* but not *<subfolderpath>/anyfilename.txt*
- Ignores all txt files including important.txt - when conflicting patterns are placed successor overrides predecessor

  ```gitignore
  *.txt
  !important.txt
  # Ignores all txt files except for important.txt
  ```

  ```gitignore
  !important.txt
  *.txt
  # Ignores all text files including important.txt
  ```

## References

- [Boot.dev Git Full Course - ThePrimeagen][1]

[//]: # (These are reference links used in the body of this note)
[//]: # (These get stripped out when the markdown processor does its job)
[//]: # (There is no need to format nicely because it shouldn't be seen.)
[//]: # (Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)

  [1]: <https://www.boot.dev/courses/learn-git>
