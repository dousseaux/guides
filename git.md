# Git Guide

Pedro Dousseau
July 12th, 2017

#### Git setup

```
$ git config --global user.name "Pedro Dousseau"
$ git config --global user.email pedro@example.com
$ git config --global core.editor nano
```

#### Start new git repository

You must have your ssh keys already setup.

```
$ git init 
$ git add .
$ git commit  -m "My first commit message"
$ git remote add origin git@github.com:username/new_repo
$ git push -u origin master
```

#### Add -> Commit -> Push

You must have your ssh keys already setup.

```
# Add all changed files
$ git add .
# Commit with a message
$ git commit  -m "My commit message"
# Push to server
$ git push
```

#### Clone

```
git clone git@github.com:username/my_repo
```