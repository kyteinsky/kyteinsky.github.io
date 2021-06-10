+++
author = "Anupam Kumar"
title = "New Hugo Website"
date = "2021-06-01"
description = "Article showing how to create a Hugo website "
tags = [
    "hugo",
    "website",
    "themes",
]
categories = [
    "daily-progress",
]
series = ["Hugo"]
image = "hugo-logo-wide.svg"
asciinema = true
+++

### We create a new Hugo website in terminal and install a custom theme named Stack and create a sample page

#### Outline of steps to be performed:
* Create an empty hugo site with boilerplate code
* Initialize the site as a git repository
* Add the theme as a submodule to receive updates automatically, can be just cloned in place or downloaded and extracted at the right place manually
* Copy some example site's posts and config file to the root folder of the website to edit them without disturbing the original theme files (these files override the respective default ones)
* Edit the config.yaml file as per requirements, and delete the config.toml file that was placed by hugo
* Create a new post with sone markdown text
* host the site locally

#### This might be the right moment to commit the changes as starting point of the repository.

### Here is the asciinema terminal cast:

{{< asciinema key="hugo-setup" rows=40 preload="1" >}}
