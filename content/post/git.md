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

* touch `/usr/local/bin/gitnew.sh`
* [edit](https://code.visualstudio.com/) `/usr/local/bin/gitnew.sh` to contain...

  #!/usr/bin/env bash 
  folder=${PWD##*/}
  rm -rf README.md 
  echo "# $folder" >> README.md 
  cp ~/.gitignore . 
  git init 
  git add README.md 
  git commit -m "first commit" 
  git remote add origin https://github.com/carlca/$folder.git 
  git push -u origin master

* Optionallly, create an alias simply called gitnew by putting `alias
  gitnew='gitnew.sh'` in your shell config file
