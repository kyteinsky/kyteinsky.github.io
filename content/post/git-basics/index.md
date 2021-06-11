+++
author = "Anupam Kumar"
title = "Git Add, Git Commit and Git Push"
date = "2021-06-11"
description = "Adding selected files and committing them with a meaningful message, lastly pushing them to remote repository."
tags = [
    "git",
    "add",
    "commit",
    "push",
]
categories = [
    "daily-updates",
]
series = ["Git"]
aliases = ["git-basics"]
asciinema = true
image = 'roman-synkevych-UT8LMo-wlyk-unsplash.jpg'
+++

## How to add and commit changes in the and then push the changes to a remote repository (here GitHub)

#### Outline of steps to be performed:
* Change some files or create some
* Issue``` git add <name of files> ```command to add files to be committed
* Then commit them using ``` git commit -am <commit message> ```, here a is for all files, m is for message, and then you place your message for the changes
* (optional) Finally, push the changes to a remote repository

## Let's see this in action

{{< asciinema key="git-basics/git-acp.cast" rows=35 preload="1" >}}
