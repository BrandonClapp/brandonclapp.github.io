---
layout: post
title:  "Git guide and useful commands"
date:   2016-05-28 09:15:00 -0500
categories: git github
---
Git can be a very powerful tool for developers if they know what they're doing, However, if they don't, the workflow feels more like an obstacle. Although I agree that it can take a bit to understand the fundamentals of git, I strongly believe that learning it is a great investment. Lets start with some basic terminology. Don't get discouraged if you don't understand them right now.

### Terminology

**Commit:** A snapshot of changes to files that have been modified. Before making a commit, the files must be *staged*. This means that they must be tracked and added to the staging area by using the `git add <filename>` command.

**Staging area:** Before making a commit, tracked files must be *staged*. Modified, deleted, added, or moved files that are tracked are eligible to be staged. The `git add` command can be used to stage these modifications. Once staged, the staged files are eligible to be committed with the `git commit` command.

**Remote:** Because git is a distributed source control system, this means that the source control resides on both remotely and local. For this reason, any repository that is located remotely (i.e. on github) is considered to be a remote repository.

**Local:** The opposite of remote. This is the repository as it lives locally. You can *clone* a remote repository locally or use `git init` to initialize a local repository. Local repositories can be linked to remote repositories.

**HEAD:** In git, HEAD refers to the last commit on the current branch.

**Clone:** The process of pulling the source code from a repository hosted remotely to your local machine, creating a local repository, and then associating that local repository with the remote that it was pulled/cloned from.

**Branch:** A branch can be thought of as an alternate timeline for your repository's code. By default, when initializing or cloning a repository, a master branch will be created. Creating a new branch will house commits/changes that are not present on the master branch until they are eventually merged back in.

**Fast-forward:** When merging, if the target branch does not contain commits outside of those from the incoming source branch, then the target can simply "fast-forward" it's HEAD to the latest commit on the source branch once it is merged.

**Recursive merge:** When merging, unlike the fast forward, if the target branch contains commits not included by the source branch, a recursive strategy is needed. This will merge all commits into the single target branch and then create a single "merge commit" stating that the branches have merged back together.

**Tags:** Tags are references to specific commits and can be useful for specifying the state of your project at any given point. For example, a tag called v1.0 could be created to represent a specific version of the project and make it easier to reference this commit rather than remembering a commit identifier.

**Rebase:** Can be used to integrate changes from one branch to another, much like merge. Rebase will do 3 things.
- Move all commits that are on the current local branch to a temporary area.
- Add all of the commits from the specified source branch (from branch) to the current branch.
- Add all of the commits from the aforementioned temporary area back onto the current branch.

------

### Testing!

```javascript
  function() {
    let i = 0;
    console.log(i);
  }
```

### Setup & Basics

##### **`git init`**

Initializes a local git repository

##### **`git config --global user.name "Brandon Clapp"`**

Sets the global git user name to be used when making commits.

##### **`git config --global user.email me@myemail.com`**

Sets the global git email to be used when making commits.

##### **`git config --global color.ui true`**

Sets the global git setting for adding pretty colors on command line

##### **`git status`**

Check which tracked files have been modified since the last commit, as well as untracked files..

------

### Adding files to staging

##### **`git add .`** or **`git add --all`**

Add all files and folders that are part of your current working directory to the staging area.

##### **`git add <filename>`**

Add a single file to the staging area.

##### **`git add <list of files>`**

Adds multiple files. Files are delimited by spaces.

##### **`git add *.<ext>`**

Adds all files with the provided extension to the staging area. i.e. `git add *.txt`

##### **`git add <folder>/*.<ext>`**

Adds all files in the docs folder with a *.txt* extension to the staging area. i.e. `git add docs/*.txt`

##### **`git add "*.<ext>"`**

Adds all files of a the provided extension in *the whole project* to the staging area.

##### **`git add <foldername>`**

Add a single folder and all associated modified files/folders in that folder to the staging area.

------

### Removing files from staging

##### **`git reset HEAD <file>`**

Removes the file from staging, but keeps any modifications made to that file.

### Undoing changes

##### **`git checkout -- <file>`**

Undo all changes to a specific file. i.e. `git checkout -- README.md`

##### **`git checkout .`**

Undo all changes for all tracked files and folders in the current working directory

##### **`git reset --hard HEAD`**

Undo all changes for all files and folders (including untracked) back to the HEAD commit.

------

### Creating a commit

##### **`git commit -m "Create a README."`**

Creates a commit of all files that are currently in the staging area.

The `-m` flag followed by "Create a README" represents the message that will be associated with the commit, or the *commit message*.

Once committed, this "snapshot" of changes is added to the top of your timeline tree.

##### **`git commit -a -m "My commit message."`** or **`git commit -am "My commit message."`**

Add all tracked files in the current working directory to staging **and** commit them with the message *My commit message* in a single step. This however, does not include untracked files.

------

### Undoing a commit

##### **`git reset --soft HEAD^`** (potentially dangerous)

Undo the last commit (HEAD) and move everything from that commit back into staging on the commit before it (HEAD^).

*Caution:* Never do this when the commit has already been pushed to a remote repository. Rewriting history is dangerous.

##### **`git reset --hard HEAD^`** (potentially dangerous)

Undo last commit and all pending changes to tracked files.

*Caution:* Never do this when the commit has already been pushed to a remote repository. Rewriting history is dangerous.

##### **`git reset --hard HEAD^^`** (potentially dangerous)

Undo last 2 commits and all pending changes to tracked files.

*Caution:* Never do this when the commit has already been pushed to a remote repository. Rewriting history is dangerous.

------

### Amending to a commit

##### **`git commit --amend -m "My new commit message"`** (potentially dangerous)

Add to the last commit. Optionally, a new commit message can be provided with `-m`, which will overwrite the previous commit message.

*Caution:* Never do this when the commit has already been pushed to a remote repository. Rewriting history is dangerous.

------

### Viewing commit history

##### **`git log`**

Shows the latest commits and information about them, including their commit message and date.

------

### Checking File Modifications

##### **`git diff`**

Show unstaged differences since last commit.

##### **`git diff --staged`**

Show differences on staged files since last commit.
