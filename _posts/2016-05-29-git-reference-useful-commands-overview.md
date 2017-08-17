---
layout: post
title:  "Git Reference: Useful Commands & Overview"
date:   May 29, 2016
categories: Git
tags:
  - Git
  - GitHub
comments: true
header: git.png
---

This guide is not intended to be a comprehensive resource, but merely an overview and reference for myself. If you feel that you can contribute helpful commands or improve this reference guide, let me know in the comments.

- [Terminology](#terminology)
- [Setup & basics](#setup-basics)
- [Adding files to staging](#adding-files-to-staging)
- [Removing files from staging](#removing-files-from-staging)
- [Undoing changes](#undoing-changes)
- [Creating a commit](#creating-a-commit)
- [Undoing a commit](#undoing-a-commit)
- [Amending to a commit](#amending-to-a-commit)
- [Viewing commit history](#viewing-commit-history)
- [Check file modifications (diff)](#check-file-modifications)
- [Managing remotes](#managing-remotes)
- [Pushing to remote repositories](#pushing-to-remote-repositories)
- [Getting changes from a remote repository](#getting-changes-from-a-remote-repository)
- [Cloning a remote repository](#cloning-a-remote-repository)
- [Branching & merging](#branching-merging)
- [Tagging](#tagging)
- [Rebase](#rebase)

Git can be a very powerful tool for developers if they know what they're doing, However, if they don't, the workflow feels more like an obstacle. Although I agree that it can take a bit to understand the fundamentals of git, I strongly believe that learning it is a great investment. Lets start with some basic terminology. Don't get discouraged if you don't understand them right now.

### [Terminology](#terminology){:name="terminology"}

**Commit:** A snapshot of changes to files that have been modified. Before making a commit, the files must be *staged*. This means that they must be tracked and added to the staging area by using the `git add <filename>` command.

**Staging:** Before making a commit, tracked files must be *staged*. Modified, deleted, added, or moved files that are *tracked* are eligible to be staged. The `git add` command can be used to stage these modifications. Once staged, the staged files are eligible to be committed with the `git commit` command.

**Tracked file/folder:** A file or folder that your git repository knows to watch for modifications on/in. Files can be untracked by adding them to a **.gitignore** file.

**Remote repository:** Because git is a distributed source control system, source control for the same project can reside in many different places at once. For this reason, any repository that is located remotely (i.e. on github) is considered to be a remote repository. Remote repository's can be cloned (copied) locally, creating a local copy of the repository known as a local repository. Once changes are made in the local repository, you can push changes back up to the remote repository, where other collaborators on the project will be able to see your changes.

**Local repository:** The opposite of remote. This is the repository as it lives locally. You can *clone* a remote repository locally or use `git init` to initialize a local repository. Local repositories can be linked to remote repositories.

**HEAD:** In git, HEAD refers to the last commit on the current branch.

**Clone:** The process of pulling the source code from a repository hosted remotely to your local machine, creating a local repository, and then associating that local repository with the remote that it was pulled/cloned from.

**Branch:** A branch can be thought of as an alternate timeline for your repository's code. When initializing or cloning a repository, a master branch will be created. Creating a new branch will house commits (snapshotted changes) that are not present on the master branch until they are eventually merged back in.

**Fast-forward:** When merging, if the target branch (where you're merging **to**) does not contain commits outside of those from the incoming source branch (where you're merging **from**), then the target can simply "fast-forward" it's HEAD to the latest commit on the source branch once it is merged.

**Recursive merge:** When merging, unlike the fast forward, if the target branch contains commits not included by the source branch, a recursive strategy is needed. This will merge all commits into the single target branch and then create a single "merge commit" stating that the branches have merged back together.

**Tags:** Tags are references to specific commits and can be useful for specifying the state of your project at any given point. For example, a tag called v1.0 could be created to represent a specific version of the project and make it easier to reference this commit rather than remembering a commit identifier.

**Rebase:** Can be used to integrate changes from one branch to another, much like merge. Rebase will do 3 things.
- Move all commits that are on the current local branch to a temporary area.
- Add all of the commits from the specified source branch (from branch) to the current branch.
- Add all of the commits from the aforementioned temporary area back onto the current branch.

### [Setup & Basics](#setup-basics){:name="setup-basics"}

**`git init`**

Initializes a local git repository

**`git config --global user.name "Brandon Clapp"`**

Sets the global git user name to be used when making commits.

**`git config --global user.email me@myemail.com`**

Sets the global git email to be used when making commits.

**`git config --global color.ui true`**

Sets the global git setting for adding pretty colors on command line

**`git status`**

Check which tracked files have been modified since the last commit, as well as untracked files..

------

### [Adding files to staging](#adding-files-to-staging){:name="adding-files-to-staging"}

**`git add .`** or **`git add --all`**

Add all files and folders that are part of your current working directory to the staging area.

**`git add <filename>`**

Add a single file to the staging area.

**`git add <list of files>`**

Adds multiple files. Files are delimited by spaces.

**`git add *.<ext>`**

Adds all files with the provided extension to the staging area. i.e. `git add *.txt`

**`git add <folder>/*.<ext>`**

Adds all files in the docs folder with a *.txt* extension to the staging area. i.e. `git add docs/*.txt`

**`git add "*.<ext>"`**

Adds all files of a the provided extension in *the whole project* to the staging area.

**`git add <foldername>`**

Add a single folder and all associated modified files/folders in that folder to the staging area.

------

### [Removing files from staging](#removing-files-from-staging){:name="removing-files-from-staging"}

**`git reset HEAD <file>`**

Removes the file from staging, but keeps any modifications made to that file.

### [Undoing changes](#undoing-changes){:name="undoing-changes"}

**`git checkout -- <file>`**

Undo all changes to a specific file. i.e. `git checkout -- README.md`

**`git checkout .`**

Undo all changes for all tracked files and folders in the current working directory

**`git reset --hard HEAD`**

Undo all changes for all files and folders (including untracked) back to the HEAD commit.

------

### [Creating a commit](#creating-a-commit){:name="creating-a-commit"}

**`git commit -m "Create a README."`**

Creates a commit of all files that are currently in the staging area.

The `-m` flag followed by "Create a README" represents the message that will be associated with the commit, or the *commit message*.

Once committed, this "snapshot" of changes is added to the top of your timeline tree.

**`git commit -a -m "My commit message."`** or **`git commit -am "My commit message."`**

Add all tracked files in the current working directory to staging **and** commit them with the message *My commit message* in a single step. This however, does not include untracked files.

------

### [Undoing a commit](#undoing-a-commit){:name="undoing-a-commit"}

**`git reset --soft HEAD^`** (potentially dangerous)

Undo the last commit (HEAD) and move everything from that commit back into staging on the commit before it (HEAD^).

*Caution:* Never do this when the commit has already been pushed to a remote repository. Rewriting history is dangerous.

**`git reset --hard HEAD^`** (potentially dangerous)

Undo last commit and all pending changes to tracked files.

*Caution:* Never do this when the commit has already been pushed to a remote repository. Rewriting history is dangerous.

**`git reset --hard HEAD^^`** (potentially dangerous)

Undo last 2 commits and all pending changes to tracked files.

*Caution:* Never do this when the commit has already been pushed to a remote repository. Rewriting history is dangerous.

------

### [Amending to a commit](#amending-to-a-commit){:name="amending-to-a-commit"}

**`git commit --amend -m "My new commit message"`** (potentially dangerous)

Add to the last commit. Optionally, a new commit message can be provided with `-m`, which will overwrite the previous commit message.

*Caution:* Never do this when the commit has already been pushed to a remote repository. Rewriting history is dangerous.

------

### [Viewing commit history](#viewing-commit-history){:name="viewing-commit-history"}

**`git log`**

Shows the latest commits and information about them, including their commit message and date.

------

### [Check file modifications](#check-file-modifications){:name="check-file-modifications"}

**`git diff`**

Show unstaged differences since last commit.

**`git diff --staged`**

Show differences on staged files since last commit.

-----

### [Managing remotes](#managing-remotes){:name="managing-remotes"}

**`git remote add <remote_name> <repo_url>`**

Associates a local repository with a remote repository.

Where *remote_name* represents any arbitrary name that you would like to reference the remote repository by (origin is commonly used) and *repo_url* represents the URL to your remote repository, which can be found on your remote hosting provider (i.e. github or bitbucket)

i.e. `git remote add origin https://github.com/BrandonClapp/process-editor.git`

**`git remote rm <remote_name>`**

Removes a remote repository reference from our local repository.

**`git remote -v`**

Check which remote repositories are associated/linked with a local repository.

**`git remote show <remote_name>`**

- Shows all remote branches and whether they're being tracked by a corresponding local branch.
- Shows which remote branch each local branch merges with for `git pull`
- Shows which remote branch each local branch pushes to for `git push`
- Shows if any local branch is out of date with it's corresponding remote branch.

**`git push <remote_name> :<remote_branch>`**

Deletes a *remote* branch. However, the local branch will still exist. Remember to remove it with `git branch -d <local_branch>`

**`git remote prune <remote_name>`**

Removes references to stale/deleted remote branches.

------

### [Pushing to remote repositories](#pushing-to-remote-repositories){:name="pushing-to-remote-repositories"}

**`git push <remote_name> <remote_branch>`**

Pushes the currently checked out local branch to the specified remote branch on the remote repository. *remote_name* is the same value that is provided when adding the remote, and, in most examples is referred to as *origin*

Optionally, the `-u` flag can be provided to set the upstream, resulting in not having to provide the remote name and branch on subsequent pushes. i.e. `git push -u origin master`.

If the remote branch that you have specified does not exist in the remote repository, it will be created. This will also link the current local branch to the remote branch that is being pushed to and, as a result, will be the default upstream branch that will be used when simply running `git push` without specifying a remote repository or branch.

------

### [Getting changes from a remote repository](#getting-changes-from-a-remote-repository){:name="getting-changes-from-a-remote-repository"}

**`git pull <remote_name> <remote_branch>`**

Gets the latest commits from the remote repository on the specified remote branch **and** merges those changes into your current local branch.

`git pull` is the equivalent of performing the following actions
- `git fetch`
- `git merge origin master`

**`git fetch`** or **`git fetch <remote_name>`**

Fetch the latest information about the remote repository such as commits and remote branches. Unlike `git pull`, this will not merge any commits into your current branch.

Tip: After fetching the remote branch, you can easily create a local branch that is already tracking the remote branch by simply running `git checkout <remote_branch>`.

-----

### [Cloning a remote repository](#cloning-a-remote-repository){:name="cloning-a-remote-repository"}

**`git clone <remote_url>`**

Pulls the source code from a repository hosted remotely to your local machine, creating a local repository, and then associating that local repository with the remote that it was pulled/cloned from.

This remote reference will be added as *origin*.

**`git clone <remote_url> <local_folder_name>`**

Clones the remote repository and puts it in the specified local folder rather than the default name of the repository.

------

### [Branching & Merging](#branching-merging){:name="branching-merging"}

**`git branch <local_branch>`**

Create a branch on the local repository. i.e. `git branch styling` might be used for changing styles. This, however, will not change to that new branch.

**`git branch -d <local_branch>`**

Deletes the specified local branch. i.e. `git branch -d styling`.

**`git branch -D <local_branch>`**

Deletes the specified local branch *and* all un-pushed commits to a remote branch.

This is helpful for when a remote branch has been deleted and you want to remove the local branch but have commits that were never pushed to the remote branch.

**`git branch`**

Lists all of the current local branches. The current branch will be indicated by an asterisk, and optionally color formatted.

**`git branch -r`**

Lists all *remote* branches.

**`git checkout <local_branch>`**

Checks out (changes to) the specified branch.

**`git checkout -b <local_branch>`**

Both creates **and** checks out (changes to) the branch specified. This is essentially a combination of `git branch <branch_name>` and `git checkout <branch_name>` in a single command.

**`git merge <from_branch>`**

This will merge the commits on a different local branch into the branch which we currently have checked out.

For example, if I have committed 5 commits to a *styling* branch and want to merge those back to the master branch, I would first ensure that I have the master branch checked out with `git checkout master` and then merge the styling branch into master with `git merge styling`.

Tip: Upon fixing a merge conflict, using `git commit -a` will auto-generate a new commit message displaying the conflicted files that were resolved.

------

### [Tagging](#tagging){:name="tagging"}

**`git tag`**

List all of the tags for the repository.

**`git checkout <tag_name>`**

Checks out the commit that the tag represents. i.e. `git checkout v1.0`

**`git tag -a <tag_name> -m "My tag description message."`**

Creates a tag and provides it a message. i.e. `git tag -a beta3 -m "it's beta, expect bugs!"`

**`git push --tags`**

Pushes tags to the remote repository.

------

### [Rebase](#rebase){:name="rebase"}

**`git rebase <from_branch>`**

Applies the commits from the *from_branch* onto the currently checked out local branch, and then applies any commits not present on the *from_branch* on top of those.

**`git rebase --continue`**

When a merge conflict is detected from a rebasing, after fixing the conflict, this will continue the rebase process.

Note: When a merge conflict occurs during a rebase, the result will be that you are no longer on a particular branch.

**`git rebase --skip`**

When a merge conflict is detected from rebasing, `--skip` can be used to skip applying the commit that resulted in merge conflict to the rebase operation. (The commit from the target/current branch will not be applied)

**`git rebase --abort`**

When a merge conflict is detected from rebasing, use the `--abort` to abandon all hope and give up, resulting in the rebase operation being rolled back.
