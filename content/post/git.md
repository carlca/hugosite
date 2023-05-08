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
* [Oh shit, git!]({{<ref "#shitgit" >}})

#### Some sanity preserving tips on Linus' monstrous creation

There's no two ways about it, Git, to some of us at least, is a struggle. It seems to fail the oft
overlooked
[Principle of Least Astonishment](https://en.wikipedia.org/wiki/Principle_of_least_astonishment)
more often than is strictly necessary. So, with this in mind, here are some common head scratchers
and the best solutions I have found so far.

#### Prerequisites / Assumptions

* Install Homebrew
    * /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

* I like to keep my own scripts in a new folder below my user folder `~bin` and make sure that it is included in my $PATH.
* As of macOS 10.15 Catalina, Zsh is supplied as the default shell in place of Bash.
* In order to keep using `HTTPS` rather than `SSH`, do the following...
    * Install GitHub CLI: `brew install gh`
    * then enter: `gh auth login`
        * Select `github.com`
        * Select `HTTPS`
        * Enter `Y` to `Authenticate Git with your GitHub credentials?`
        * Select `Login with a web browser`
        * Copy one-time code
        * Paste in activation code into `github.com` in browser
        * Confirm by pressing `Authorize github`
        * Congratulations, you're all set 👍
    * This will supress the

* Make sure that you have set up your Git credentials...
    * git config --global user.name "Your Name"
    * git config --global user.email "youremail@yourdomain.com"

#### How to create a script to make a new Git repository {#gitnew}

These instructions have been revised now that I have worked out how to use shell scripts to make the job ridiculously easy.

Follow these steps:

* Create a default `.gitignore` file and place it in `~`
* `touch ~/bin/gitnew.sh`
* `chmod 755 ~/bin/gitnew.sh`
* [edit](https://code.visualstudio.com/) `~/bin/gitnew.sh`

##### to contain...

    #!/bin/zsh

    # get the CWD and put it in $folder  
    folder=${PWD##*/}

    # make sure that >>! works
    set -o noclobber

    # I'm sure there are more concise ways of doing this...
    if [ ! -e README.md ]; then
        touch README.md
        chmod 755 README.md
    fi
    echo "# $folder" >> README.md

    # delete previous .gitignore, if any. This deals with cp not supressing override prompts!
    rm -rf .gitignore

    # copy default .gitignore file from ~
    cp ~/.gitignore .

    # this command has been tweaked to make it entirely portable
    gitname=`git config user.name`

    # instructions as per https://github.com/new
    git init

    # note neat trick to supress message if remote rm fails
    (git remote rm origin) 2>/dev/null

    git remote add origin https://github.com/$gitname/$folder.git

    git branch -M main
    git add README.md
    git commit -m "first commit"

    # the final push!
    git push -u origin main
    
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

* `touch ~/bin/gitadd.sh`
* `chmod 755 ~/bin/gitadd.sh`
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

#### Oh shit, git! {#shitgit}

The wonderful Katie Sylor-Miller [@ksylor](https://twitter.com/ksylor) and [@ohshitgit](https://twitter.com/ohshitgit) has put together a marvellous site full of intelligence, insight, attitude and profanity. See it at ohshitgit.com
