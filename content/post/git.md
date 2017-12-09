---
banner: "" 
categories: ["DevOps"] 
date: "2017-12-08T12:18:17Z" 
description: "Some sanity preserving tips
on Linus' monstrous creation"
draft: false
images: [] 
menu: "" 
tags: ["git"] 
title: "Git"
---

#### Index

* [How to create a script to make a new Git repository]({{<ref "#gitnew" >}})
* [How to create a script to update an existing Git repository]({{<ref
  "#gitadd" >}})

#### Some sanity preserving tips on Linus' monstrous creation

There's no two ways about it, Git, to some of us at least, is a struggle. It
seems to fail the oft overlooked
[Principle of Least Astonishment](https://en.wikipedia.org/wiki/Principle_of_least_astonishment)
more often than is strictly necessary. So, with this in mind, here are some
common head scratchers and the best solutions I have found so far.

#### <a name="gitnew">

</a>How to create a script to make a new Git repository

These instructions have been revised now that I have worked out how to use shell
scripts to make the job ridiculously easy.

Follow these steps:

* Create a default `.gitignore` file and place it in `~`
* `touch /usr/local/bin/gitnew.sh`
* `chmod 755 /usr/local/bin/gitnew.sh`
* [edit](https://code.visualstudio.com/) `/usr/local/bin/gitnew.sh`

##### to contain...

    #!/usr/bin/env bash

    # get the CWD and put it in $folder
    folder=${PWD##*/}

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
    gitname=`git config user.name`
    git remote add origin https://github.com/$gitname/$folder.git

    # the final push!
    git push -u origin master

* Optionally, create an alias simply called gitnew by putting `alias
  gitnew='gitnew.sh'` in your shell config file

#### How to use the script

Before using the script, it is assumed that you have created a new working
folder for your source code and that your Github repository will have the same
name.

* Create the repository in Github, but do not do any of the follow up steps
  described on https://github.com/new. They are all done by the shell script.
* `cd` to the working folder and type `gitnew` or `gitnew.sh` ⏎ depending on
  whether you created an alias.

<a name="gitadd"></a>

### How to create a script to update an existing Git repository

Follow these steps:

* `touch /usr/local/bin/gitadd.sh`
* `chmod 755 /usr/local/bin/gitadd.sh`
* [edit](https://code.visualstudio.com/) `/usr/local/bin/gitadd.sh`

##### to contain...

    #!/usr/bin/env bash

    # update/add files recursively
    git add -A .

    # commit with user supplied message
    git commit -m "$1"

    # the final push!
    git push

* Optionally, create an alias simply called gitadd by putting `alias
  gitadd='gitadd.sh'` in your shell config file

#### How to use the script

* Make your code changes or create new files/folders using your favorite
  [editor](https://code.visualstudio.com/)
* Type `gitadd "<commit message>"` or `gitadd.sh "<commit message>"` ⏎ depending
  on whether you created an alias.

#### Summary

This makes the basic workflow for Git very easy...

* Create Git repository at https://github.com
* Type `gitnew` or `gitnew.sh` ⏎
* Make code changes or create new files/folders
* Type `gitadd "<commit message>"` or `gitadd.sh "<commit message>"` ⏎
