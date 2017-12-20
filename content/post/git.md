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
* [Oh shit, I did something terribly wrong, please tell me git has a magic time machine!?!]({{<ref "#shitmagic" >}})
* [Oh shit, I committed and immediately realized I need to make one small change!]({{<ref "#shitsmall" >}})
* [Oh shit, I need to change the message on my last commit!]({{<ref "#shitlast" >}})
* [Oh shit, I accidentally committed something to master that should have been on a brand new branch!]({{<ref "#shitnew" >}})
* [Oh shit, I accidentally committed to the wrong branch!]({{<ref "#shitwrong" >}})
* [Oh shit, I tried to run a diff but nothing happened?!]({{<ref "#shitnothing" >}})
* [Fuck this noise, I give up.]({{<ref "#shitfuck" >}})

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

#### Oh shit, git! {#shitgit}

Git is hard: screwing up is easy, and figuring out how to fix your mistakes is fucking impossible. Git documentation has this chicken and egg problem where you can't search for how to get yourself out of a mess, unless you already know the name of the thing you need to know about in order to fix your problem.

So here are some bad situations I've gotten myself into, and how I eventually got myself out of them.

#### Oh shit, I did something terribly wrong, please tell me git has a magic time machine!?! {#shitmagic}

    git reflog
    # you will see a list of every thing you've done in git, across all branches!
    # each one has an index HEAD@{index}
    # find the one before you broke everything
    git reset HEAD@{index}
    # magic time machine
    You can use this to get back stuff you accidentally deleted, or just to remove some stuff you tried that broke the repo, or to recover after a bad merge, or just to go back to a time when things actually worked. I use reflog A LOT. Mega hat tip to the many many many many many people who suggested adding it!

#### Oh shit, I committed and immediately realized I need to make one small change! {#shitsmall}

    # make your change
    git add . # or add individual files
    git commit --amend
    # follow prompts to change or keep the commit message
    # now your last commit contains that change!

This usually happens to me if I commit, then run tests/linters... and FML, I didn't put a space after the equals sign. You could also make the change as a new commit and then do rebase -i in order to squash them both together, but this is about a million times faster.

#### Oh shit, I need to change the message on my last commit! {#shitlast}

    git commit --amend
    # follow prompts to change the commit message

Stupid commit message formatting requirements.

#### Oh shit, I accidentally committed something to master that should have been on a brand new branch! {#shitnew}

    # create a new branch from the current state of master
    git branch some-new-branch-name
    # remove the commit from the master branch
    git reset HEAD~ --hard
    git checkout some-new-branch-name
    # your commit lives in this branch now :)

Note: this doesn't work if you've already pushed to origin, and if you tried other things first, you might need to git reset HEAD@{number} instead of HEAD~. Infinite sadness. Also, many many many people suggested an awesome way to make this shorter that I didn't know myself. Thank you all!

#### Oh shit, I accidentally committed to the wrong branch! {#shitwrong}

    # undo the last commit, but leave the changes available
    git reset HEAD~ --soft
    git stash
    # move to the correct branch
    git checkout name-of-the-correct-branch
    git stash pop
    git add . # or add individual files
    git commit -m "your message here"
    # now your changes are on the correct branch

A lot of people have suggested using cherry-pick for this situation too, so take your pick on whatever one makes the most sense to you!

    git checkout name-of-the-correct-branch
    # grab the last commit to master
    git cherry-pick master
    # delete it from master
    git checkout master
    git reset HEAD~ --hard

#### Oh shit, I tried to run a diff but nothing happened?! {#shitnothing}

    git diff --staged

Git won't do a diff of files that have been add-ed to your staging area without this flag. File under ¯\_(ツ)\_/¯ (yes, this is a feature, not a bug, but it's baffling and non-obvious the first time it happens to you!)

#### Fuck this noise, I give up. {#shitfuck}

    cd ..
    sudo rm -r fucking-git-repo-dir
    git clone https://some.github.url/fucking-git-repo-dir.git
    cd fucking-git-repo-dir

Thanks to @viaz66 for this one.
