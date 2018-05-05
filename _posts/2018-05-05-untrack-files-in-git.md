---
layout: post
cover: 'assets/images/git_1.png'
title: Untrack files in Git
date:   2018-05-05 00:00:00
tags: git
subclass: 'post tag-fiction'
categories: 'casper'
navigation: True
logo: 'assets/images/git_logo.png'
---

Adding file paths to `.gitignore` in a project untracks them. Changes in those files will not be tracked and we are good to go. What if we do need a version of a file to be pushed in the repository? Say in our project we have a file `keys` which contains the a pair of keys. We do not wish to push the credentials to our repo, but we would like to keep the file `keys` in our repository.

Running `git rm --cached <file-path>` will remove the path from index. The subsequent push will delete the file from remote. It did not help me. So here is what I did. 

- Remove the existing file containing the credentials from `.gitignore`
- Replace the credentials in the file with dummy. Commit the sample file and push it to remote. 
- Now run `git update-index --assume-unchanged <file-path>` for the file. Add your credentials to the file and you are good to go. The further changes in our file are not tracked. Running `git update-index --no-assume-unchanged <file-path>` will bring the file back to being tracked. 

From the [git documentation](https://git-scm.com/docs/git-update-index#git-update-index---no-assume-unchanged)


		When this flag is specified, the object names recorded for the paths are not updated. Instead, this option sets/unsets the "assume unchanged" bit for the paths. When the "assume unchanged" bit is on, the user promises not to change the file and allows Git to assume that the working tree file matches what is recorded in the index. If you want to change the working tree file, you need to unset the bit to tell Git.

