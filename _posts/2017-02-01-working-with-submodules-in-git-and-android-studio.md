---
layout: post
cover: 'assets/images/cover9.jpg'
title: Working with submodules in Android Studio and git
date:   2017-02-01 10:18:00
tags: development
subclass: 'post tag-fiction'
categories: 'casper'
navigation: True
logo: 'assets/images/ghost.png'
---

I encountered working with submodules in Android Studio a while back while I was integrating GIS features with our [PDM](https://github.com/sunny5125/pdm) app. Apparently it looked like a simple task, and truth be told it was, I did face a few hiccups getting there. The following will be a documentation of the procedure I followed while I integrated [NextGis Android App](https://github.com/nextgis/android_gisapp) with our very own college project. 

### The Basics 

Android Studio provides the option to add sub modules to our existing project. What it means you can import another project (your own or maybe some other repository) and integrate it with your project with ease. As in my case I had to integrate [this](https://github.com/nextgis/android_gisapp) repository. Looking closely you will observe that the NextGis android app repository has 2 submodules : [maplib](https://github.com/nextgis/android_maplib) and [maplibui](https://github.com/nextgis/android_maplibui). 

So I started out forking android_gisapp repository. In order to initialise the submodules within android_gisapp I had to do : 

        git submodule init
        git submodule update
 
and voila, git clones the required submodules into android_gisapp and we are good to go. However for our research work we needed to change some core features of android_gisapp and soon realised it will be better to keep my own forks of maplib and maplibui. 

At this point it is better to get acquainted with submodules in git, the best blog which I could find is [here](https://johnleach.co.uk/words/323/git-submodules-in-n-easy-steps). Changing the submodules in my fork of android_gisapp to point to my forks of maplib and maplibui was simple, just change the path in `.gitmodules` and do `git submodule update`. Finally push the changes. 

### Changing a project to build as a library

This was the part in which I was stuck the most. I realised we cannot use an `apk` as a dependency in android project and it supports only `jar` and `aar` dependencies as of now. The alternative was to compile the android_gisapp as a library and include the `aar` file in dependency. I decided to keep my fork of android_gisapp as a module in our PDM app. That way we can change the submodules as we are developing PDM. So the project modules in PDM will look like

        app
         |
         |--android_gisapp
 	         |
 	         |--maplib
 	         |--maplibui

Before adding android_gisapp as a submodule since an android project cannot have two modules with same name I had to change the main module name of android_gispp from `app` to `android_gisapp`. To do this we can use the refractor option in android studio or change in `settings.gradle`

        include ':app', ':maplib', ':maplibui'
 
to 

        include ':gisapp', ':maplib', ':maplibui'

After that I had to build android_gisapp as a library project. We cannot have two different apks for the same project and changes had to be made in this regard. To build project as a library head to `build.gradle` and change 

        apply plugin: 'com.android.application'
 
to 

        apply plugin: 'com.android.library'

We have to remove `applicationId` under `defaultConfig` and also the following section : 

        applicationVariants.all { variant ->
                 variant.outputs.each { output ->
                     output.outputFile = new File(output.outputFile.parent, "ngmobile-" + defaultConfig.versionName + ".apk")
                 }
             }

Change category of the `LAUNCHER` activity to `DEFAULT` (in general it is likely that you will have a `LAUNCHER` activity in your main project)
Then do a fresh build of the project and push the changes to remote.

### Adding an existing repository as module in PDM

The next bit was simple. I had to include my fork of android_gisapp as a submodule in PDM. In PDM directory I had to 

        git submodule add <link to my fork of android_gisapp>
        git init
        git submodule update
        cd android_gisapp
        git submodule init
        git submodule update

Then in Android Studio File->Project Structure->app->add a new module and give the path to android_gisapp. After that do a clean build.  

Hopefully this integration was proper and meaningful. Upto now it has been all good. It is super easy to maintain all 4 repositories. Two noteworthy points are 

- always explicitely checkout to a branch in a submodule in git. Failure to do this may lead to HEAD detached.
- after commiting changes in submodules we must push them. Only after that we can add the changes and reflect them in parent module. 

Like all things in git, the learning curve is steep but there are immense possibilities to get out of it once we get the basic concepts. I will keep updating this thread if I have missed some part, for any major updates or changes I will make a separate blog. Till then adios! 

### Credits 

A very big shout out to [NextGis](http://nextgis.com/) for keeping such a well documented repository to provide Opensource GIS application for android. 
