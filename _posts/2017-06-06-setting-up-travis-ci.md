---
layout: post
cover: 'assets/images/travisci.jpg'
title: Setting up Travis CI for the blog
date:   2017-06-05 00:00:00
tags: development
subclass: 'post tag-fiction'
categories: 'casper'
navigation: True
logo: 'assets/images/home.png'
---

If your blog is a fork of [Jasper](https://github.com/biomadeira/jasper/) and you host it in github then you might have noticed that author pages and tag pages returns 404 error. The reasion as mentioned in this [issue](https://github.com/biomadeira/jasper/issues/16) is that Github restricts running plugins for security reasons. [Fábio Madeira](https://github.com/biomadeira) describes two workaround for this issue and here I am going to describe the second one. 

Firstly in `_config.yml` we need to specify which repository we wish to build with travic-ci with the travis gem. The git url in github for any repository is of the form `https://github.com/USERNAME/REPO.git`. 

- Change username to your github username, 
- repo to the name of the repository (for me it is blogs), 
- branch to the name of the branch from which you want to build your project (I have kept it master), 
- relative source is the path to your project w.r.t. the configuration file (since configuration file is mostly at the root of the project it will be `../PROJECT_NAME/`) and 
- destination path where travis-ci will clone your project. 
- After this change the production_url (for hosting in github it is `https://USERNAME.github.io/REPO`) and 
- source_url (again should be of the form `https://github.com/USERNAME/REPO/`).

![Code](assets/images/s1.png)

Now we need to create the `.travis.yml` file. Follow [this](https://docs.travis-ci.com/user/languages/ruby/) to get started. Keep the ruby version to which you are using in your project. After this we need to generate a secure key with travis gem. First get your Github token from your Travis CI profile page.

		gem install travis
		travis encrypt 'GIT_NAME="YOUR_USERNAME" GIT_EMAIL="YOUR_EMAIL" GH_TOKEN=YOUR_TOKEN'

- `GIT_NAME` is your Github username,
- Obtain `GH_TOKEN` from `https://github.com/settings/tokens`. 

Add the secure key and specify the branch from where to build your project. Should look something like this:

![Code](assets/images/s2.png)

Travis CI should build from your specified branch every time you push after this setup! 

After successful build got to `https://USERNAME.github.io/REPO` and see this time the author page and tag pages should work fine.
