---
layout: post
title: "First Post: Blog Setup"
modified:
categories: 
excerpt:
tags: [jekyll, blog]
image:
  feature:
date: 2014-11-01T13:49:09-04:00
---

Welcome to my **new** blog. I emphasize the word **new** as I actually still have an **old** blog. It is currently still active and available at: [http://guylichtman.com/wordpress/](http://guylichtman.com/wordpress/), although I don't know how long this will last. My old blog somehow died out and I decided it is time to start with a fresh new start from scratch. So this my first post and what is more fitting than writing about the setup. 

My platform of choice is GitHub Pages. The great advantage of using GitHub is that everything is managed in git. Full version control is enabled on the blog, which is excellent. My blog's git repository is available [here](https://github.com/guylichtman/guylichtman.github.io). I won't go into details about setting up a GitHub Pages repository as this is well documented. 

I decided to use the [So Simple Theme](https://github.com/mmistakes/so-simple-theme) by [Michael Rose](http://mademistakes.com). So first task was to fork the repository. Next I customized the *post.html* layout so if an author's name is not specified the side header in the post with the author and avatar is not displayed. I basically did this as I will be the only one posting on this blog and I find it a bit unnecessary to include this info in each post. So now I can simply comment out the `owner.name` field in _config.yml and this info will not be displayed on each post. This change is available in the following [git commit](https://github.com/guylichtman/guylichtman.github.io/commit/8b138f05da50092a6b9119dee18c649eeb5f647a).

Next tasks were straight forward:

 - Edit `_config.yml` with proper site info and title.
 - Edit `_data/navigation.yml` to customize the navigation links at the top of the pages.
 - Change the site log picture.
 - Delete sample posts.
 - Edit about page.
 - Create a new empty post to get started by running: 
 ```octopress new post "First Post: Blog Setup"```
 - Write this blog post.
 - Commit and publish to GitHub.

Setup is now complete.


