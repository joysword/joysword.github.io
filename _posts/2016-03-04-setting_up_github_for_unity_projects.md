---
layout: post
title: "Setting Up Github (and Git LFS) for Unity projects"
category: game-dev
tags: Unity Github "Team Management"
---

## Assumptions
* your team all have Git installed
* your team is using Unity 5.0 or higher
* your team is following [Git-flow](http://nvie.com/posts/a-successful-git-branching-model/) branching model (if not, feel free to skip the steps for `develop` branch)

## Install Git LFS

For more information about Git Large File Storage (LFS), see [here](https://git-lfs.github.com).

For Mac users, installing Git LFS using [Homebrew](http://brew.sh) is recommended (your Git itself should be installed via homebrew too!). To install, just run `brew install git-lfs`.

For Windows users, download the installer from [official website](https://git-lfs.github.com) and run. After the installation process, run `git lfs install` in your git bash to activate it.

## Create the Repository (for team leader)
* install Git LFS (see Install Git LFS section)
* add file types to use Git LFS to manage by running `git lfs track "*.<file_extensions>"`
	- for example, to use Git LFS to manage `psd` files, run `git lfs track "*.psd"`
	- **Tip**: file types I use Git LFS to manage are: `psd`, `png`, `mp3`, `wav`, `prefab`, `unity`, `xcf`
	- **Note**: this step can be done by editing `.gitattributes` directly, one line one type, like this: `*.psd filter=lfs diff=lfs merge=lfs -text`
* create an empty Unity project
* change project settings (based on [this Stack Overflow post](http://stackoverflow.com/questions/21573405/how-to-prepare-a-unity-project-for-git))
	- Go to **Edit** → **Project Settings** → **Editor**
	- Change **Version Control Mode** to **Visible Meta Files**
	- Change **Asset Serialization Mode** to **Force Text**
	- Save Scene and Project
* prepare the Git repo
	- run `git init` to initialize a Git repo
	- create a `.gitignore` file for the repo by copying [this .gitignore file](https://github.com/github/gitignore/blob/master/Unity.gitignore)
	- add `.gitconfig` file for your system **OR** configure your Git client (SourceTree, etc.) to use Unity's Smart Merge (for detailed instruction, see [this official article](http://docs.unity3d.com/Manual/SmartMerge.html))
	- push branch `master` to remote
* prepare the `develop` branch
	- run `git branch develop` to create a new branch `develop`
	- run `git checkout develop` to switch to branch `develop`
	- push branch `develop` to remote
* add your team member as collaborators of your Github repository and send them step in the next section to follow

## Clone the Repository (for other team members)
* install Git LFS (see Install Git LFS section)
* prepare project folder
	- create an empty Unity project
	- quit Unity
	- **Important:** delete ProjectSetting folder
* prepare the Git repo
	- run `git init` to make this project a git repo
	- run `git remote add origin <remote_repo_address>` to add remote repo
	- pull remote branch `origin/master` to local branch `master`
	- run `git branch develop` to create a new local branch `develop`
	- run `git checkout develop` to switch to branch `develop`
	- pull remote branch `origin/develop` into local branch `develop`
* add `.gitconfig` file for your system OR configure your Git client (SourceTree, etc.) to use Unity's Smart Merge (for steps, see [this official article](http://docs.unity3d.com/Manual/SmartMerge.html))
* open the project in Unity and start to work