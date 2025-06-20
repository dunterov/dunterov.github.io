---
layout: post
title: Blog on github? What?
---

<div style="background-color: #eee; padding: 1em;">
  ⚠️ <strong>Please note</strong>: <em>The information in this legacy post may no longer be relevant.</em>
</div>
In this post I want to describe how that blog works. I've discovered it a couple of days ago, but as I see it's pretty awesome and lightweight.
First, a quick overview: what is GitHub? At its core, GitHub is a platform where you can store and manage your source code using Git. But it has grown far beyond that. Today, GitHub offers a lot of cool features, one of which is **GitHub Pages**. This feature lets you publish content using Git and standard markup languages.
So, let's walk through the steps to create a blog like this one. I hope this guide proves helpful to others!

Here are the installation steps:

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

Easy enough, right? So, now you can go to https://yourusername.github.io/postname and your post should be there.

### What's next?

Well, Now you can do what you want with your blog! Good idea is to add search and comments, tune fonts and colors and many more. But I have to admit, it's pretty clear and convinient, to have post's collection stored in .md format. Let's see how it'll be in the future.
