# Obsidian to Hugo blog builder

## Instructions to fork this repository

This repository contains my personal set up to build my Obsidian notes into a Hugo based blog. This will take 1 or 2 hours.

### Fork this repo

Yes, fork this repo.

### Prepare your Hugo blog

Either create a new hugo blog by following the instructions on their website: https://gohugo.io/getting-started/quick-start/ or import your hugo blog into `hugoroot`.
Note that this script will overwrite some files that are needed to render links and images properly. Read more here why:

https://github.com/zoni/obsidian-export/#relative-links-with-hugo

### Copy and modify the build.sh script

The build.example.sh script has a few variables you can modify, but the most important piece is to inject your Obsidian vault into the `vault` directory, and also configure the root of where the blog is located. Copy into build.sh and change the script accordingly.

### Import your vault in the build script

Again, you will need to copy your vault into the `vault` directory. I recommend using the Obsidian Git plugin to back up your notes to a private Git repository. I will use Github as a platform but anywhere else will do. Once
your Vault is exported into git, you can use git to clone / submodule into the `vault` directory.

I personally use submodules. I run:

```
rm -rf ./vault
git submodule add git@github.com/ORG/REPO ./vault
```

Then I modify the build script with:

```
git submodule init
git submodule update
```


### Add frontmatter in your Obsidian posts

Hugo requires frontmatter for the blog posts to be rendered and tagged appropriately. I use the [Templater](https://github.com/SilentVoid13/Templater) plugin to put dates and other things automatically. I created the following template:

```
---
title: <% tp.file.title %>
date: <% tp.file.creation_date("YYYY-MM-DD") %>
description:
tags:
---
```

This template is inserted on top of the posts that I want to render into a blog.

### Push this to a repository

Push all your changes to a git repo. I use Netlify to deploy the blog. I:

1. Link my fork of this repo as a Netlify project
1. Add a Deploy Key to my Obsidian vault's repo so Netlify can download it
1. Trigger a deploy. Hopefully things will be super smooth!


### Automatically deploying your blog on Obsidian updates

1. Create a Build hook in your Netlify application. You will get an URL like so: `https://api.netlify.com/build_hooks/12345"
1. Create a Github Action in your _Obsidian_ repository. Create a file named `.github/workflows/main.yml` with the following contents:

```
name: Trigger Netlify Build
on: push
jobs:
  build:
    name: Request Netlify Webhook
    runs-on: ubuntu-latest
    steps:
      - name: Curl request
        run: curl -X POST -d {} https://api.netlify.com/build_hooks/12345
```

Boom! Whenever Obsidian Git pushes the changes, your blog will be updated right after.
