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
* [How to create a script to update an existing Git repository]({{<ref "#gitadd" >}})

#### Some sanity preserving tips on Linus' monstrous creation

There's no two ways about it, Git, to some of us at least, is a struggle. It seems to fail the oft
overlooked
[Principle of Least Astonishment](https://en.wikipedia.org/wiki/Principle_of_least_astonishment)
more often than is strictly necessary. So, with this in mind, here are some common head scratchers
and the best solutions I have found so far.

#### How to create a script to make a new Git repository {#gitnew}

These instructions have been revised now that I have worked out how to use shell scripts to make the job ridiculously easy.

Follow these steps:

* Create a default `.gitignore` file and place it in `~`
* `touch /usr/local/bin/gitnew.sh`
* `chmod 755 /usr/local/bin/gitnew.sh`
* [edit](https://code.visualstudio.com/) `/usr/local/bin/gitnew.sh`

##### to contain...

    #!/usr/bin/env bash

    # get the CWD and put it in $folder  
    folder=${PWD##*/}

    # create new, or add to existing, README.md
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

* Optionally, create an alias simply called gitnew by putting `alias gitnew='gitnew.sh'` in your
  shell config file

#### How to use the script

Before using the script, it is assumed that you have created a new working folder for your source
code and that your Github repository will have the same name.

* Create the repository in Github, but do not do any of the follow up steps described on
  https://github.com/new. They are all done by the shell script.
* `cd` to the working folder and type `gitnew` or `gitnew.sh` ⏎ depending on whether you created
  an alias.

#### How to create a script to update an existing Git repository {#gitadd}

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

* Optionally, create an alias simply called gitadd by putting `alias gitadd='gitadd.sh'` in your shell config file.

#### How to use the script

* Make your code changes or create new files/folders using your favorite
  [editor](https://code.visualstudio.com/).
* Type `gitadd "<commit message>"` or `gitadd.sh "<commit message>"` ⏎ depending on whether you
  created an alias.

#### Summary

This makes the basic workflow for Git very easy...

* Create Git repository at https://github.com
* Type `gitnew` or `gitnew.sh` ⏎
* Make code changes or create new files/folders
* Type `gitadd "<commit message>"` or `gitadd.sh "<commit message>"` ⏎

Some more Git related tips from ohshitgit.com...

#### Oh shit, git!

Git is hard: screwing up is easy, and figuring out how to fix your mistakes is fucking impossible. Git documentation has this chicken and egg problem where you can't search for how to get yourself out of a mess, unless you already know the name of the thing you need to know about in order to fix your problem.

So here are some bad situations I've gotten myself into, and how I eventually got myself out of them.

#### Oh shit, I did something terribly wrong, please tell me git has a magic time machine!?!

    git reflog                  # you will see a list of every thing you've done in git, across all
                                # branches. each one has an index HEAD@{index}
                                # find the one before you broke everything
    git reset HEAD@{index}      # magic time machine

You can use this to get back stuff you accidentally deleted, or just to remove some stuff you tried that broke the repo, or to recover after a bad merge, or just to go back to a time when things actually worked. I use reflog A LOT. Mega hat tip to the many many many many many people who suggested adding it!
