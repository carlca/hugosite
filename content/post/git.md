---
banner: "" 
categories: ["DevOps"] 
date: "2017-12-08T12:18:17Z" 
description: "some sanity preserving tips
on Linus' monstrous creation"
draft: false
images: [] 
menu: "" 
tags: ["git"] 
title: "Git"
---

#### some sanity preserving tips on Linus' monstrous creation

There's no two ways about it, Git, to some of us at least, is a struggle. It
seems to fail the oft overlooked
[Principle of Least Astonishment](https://en.wikipedia.org/wiki/Principle_of_least_astonishment)
more often than is strictly necessary. So, with this in mind, here are some
common head scratchers and the best solutions I have found so far.

##### How to Add a New Repository

These instructions have been revised now that I have worked out how to use shell
scripts to make the job ridiculously easy.

Follow these steps:

* `touch /usr/local/bin/gitnew.sh`
* [edit](https://code.visualstudio.com/) `/usr/local/bin/gitnew.sh` 

##### to contain...
    #!/usr/bin/env bash 

    # get the CWD and put it in $folder
    folder=${PWD##\*/}\             

    rm -rf README.md 

    # create new README.md
    echo "# $folder" >> README.md   
    
    # copy default .gitignore file from ~
    cp ~/.gitignore .               

    # instructions as per https://github.com/new
    git init 
    git add README.md 
    git commit -m "first commit" 

    # this command has been tweaked to make it entirely portable
    gitname=$(git config github.user)
    git remote add origin https://github.com/carlca/$folder.git 

    # the final push!
    git push -u origin master

* Optionallly, create an alias simply called gitnew by putting `alias
  gitnew='gitnew.sh'` in your shell config file
