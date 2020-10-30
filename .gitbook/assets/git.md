# <h1 id="topheading">Git Stuff</h1>

## Objectives

Git tips/tricks/etc.

Note: Git is an example of a DVCS (hence Distributed Version Control System). Rather than have only one single place for the full version history of the software as is common in once-popular version control systems like CVS or Subversion (also known as SVN), in Git, every developer's working copy of the code is also a repository that can contain the full history of all changes.

##  git Getting Started

Download a project repository from a remote location; check out the most recent commit of the default branch; track the remote location as the “origin” remote
```
git clone
```

Place a snapshot of a file (new or edited) from the working tree into the staging area
```
git add
```

Send changes from the local repository to a remote repository
```
git push
```

Integrate changes from a remote repository into the local repository
```
git pull
```

Show what files have been changed in the working tree (index.txt)
```
git status
```

## To create a new local repo:
```
git init /path/to/directory
```

## Getting Started
Set up various settings (username and email are required minimal):

```
git config --global user.name "My Name"
git config --global user.email "myemail@domain.com"
git config --global core.editor "/usr/bin/nano"
git config --list
```

If you want to use different values for a different project, do the following:

```
cd fake-project
git config user.name "Jim Rohn"
git config user.email "diffemail@yahoo.com"
git config core.editor "/usr/bin/nano"
git config --list
```


## Checking project status:

View the state of your staged and upstaged files:
```
git status
```

View output in shortened format:
```
git status -v
```

Get more verbose output, including what was changed:
```
git status -v
```

## Committing to git:

Opens text editor to prepare for a commit of files in the staging area:
```
git commit
```

Bypasses the editor and performs a commit with the specified message:
```
git commit -m "Commit Message"
```

Commit a modified file in the staging area:
```
git commit -a -m "message"
```


## Ignoring Certain File Types

Original file that contains file patterns that git will not track:
```
.git/info/exclude
```

Ignore file local to a git repo commonly used to exclude files based on patters:
```
.gitignore
```

Used to debug git ignore to see what is and is not being excluded from git
```
git check-ignore <pattern>
```

Local documentation for the gitignore file:
```
man gitignore
```
## View Commit History

```
git log --oneline
```

## View Stats on who did what

```
git log --stat
git shortlog
git log --pretty="%cn committed %h on %cd"
```

## Tagging projects
This is useful when you want to mark a point in your project's history, or use a specific tag as a reference point, such as a release of a specific version

Create an annotated tag:
```
git tag -a <tag name> -m <message>
```

View all tags for the repo:
```
git tag
```

Delete a tag
```
git tag -d <tag name>
```



Links:
https://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository
https://linuxacademy.com/cp/courses/lesson/course/1967/lesson/1

A pull request is a way to ask another developer to merge one of your branches into their repository
