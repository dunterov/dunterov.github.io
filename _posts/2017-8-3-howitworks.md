---
layout: post
title: How this blog works?
---

### Blog on github? What?

Here I want to describe, how that blog works. I've discovered it a couple of days ago, but as I see it's pretty awesome and lightweight.
First, what is GitHub? GitHub is literally a hub, where you can store your source codes using git. More important is that fact, that github become a powerful portal with rich functionality. One function is "Gitbook Pages". It allows you to publish some info using git and standard markup. So, let's see step-by-step how to make a blog like this! Hope, this post will be useful for someone.

Here's an installation's steps:
* Sign Up/In on GitHub
* Go to the [Jekyll-now repository](https://github.com/barryclark/jekyll-now/)
* Fork it!
* Rename forked repository just like yourusername.github.io
* after that, edit _config.yml. File's structure is simply enough, so no problem should be appeared.
* After that, go to Settings to check from which branch is site built. If you see something like:
  
> Your GitHub Pages site is currently being built from the master branch.

than all OK. If not - select branch and press "Save"
* Now go to https://yourusername.github.io
* That's it! You're UP&Running!

### Right. So, how to make a post?

First step is to clone your new repository:
```
git clone https://github.com/username/username.github.io.git
cd username.github.io
```
Great! Now you can make your first post! Just create new file inside posts folder, just like *YYYY-M-D-filename.md*
```
touch posts/2017-8-2-postname.md
```
Basically, this file could contain only the following information:
```
$ cat posts/2017-8-2-postname.md   # <--this is how you may see content of text file

---
layout: post
title: Post header, which is visible on a page
---
Hello world!

$ git add posts/2017-8-2-postname.md
$ git commit -m "Very first post"
$ git push   
```
After that git ask your username/password pair.
Easy enough, right? So, now you can go to https://yourusername.github.io/postname and your hello should be there.

### What's next?

Well, since now you may do what you want with your blog! Good idea is to add search and comments, tune fonts and colors and many more. But I have to admit, it's pretty clear and convinient, to have you post's base in .md format. Let's see how it'll be in future.
