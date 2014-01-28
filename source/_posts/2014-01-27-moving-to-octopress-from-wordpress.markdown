---
layout: post
title: "Moving To Octopress From Wordpress"
date: 2014-01-27 23:15:22 -0600
comments: true
categories: [Octopress, Blogging, Jekyll]
---

After years of using Wordpress as my blogging platform I've decided to move to [Octopress](http://octopress.org/).  While there was not necessarily anything wrong with using Wordpress for my blog, I think it was time to move on.  Octopress is a blogging framework that is built around the [Jekyll.rb](http://jekyllrb.com/) project that generates static html sites.

Here's why I switched:

- ***It's Ruby!*** - Since I spent most of my time coding in Ruby it only made sense to move to a Ruby based blogging platform.
- ***Focus On Content*** - I found on my old Wordpress blog, I spent a lot of time configuring my theme and worrying about all the other pages of the site.  I decided to concentrate on blog posts.  The new setup has the content at the forefront.
- ***Free Hosting*** - Octopress is built on top of [Jekyll.rb](http://jekyllrb.com/) which powers [Github Pages](http://pages.github.com/). Github Pages hosting is free. Can't beat that.
- ***Easy Publishing*** - Creating and publishing a blog post is as easy as creating a Markdown file, writing my content and then simply running ```rake deploy```
- ***Site Speed*** - Octopress sites are just static html.  No databases to load.  Much faster.
- ***No More DB Backups*** - Since I'm deploying the site with Github Pages, I don't need to worry about backing up the content in the database.  The entire thing is in version control with Git.
- ***Awesome Tech*** - I get to use SASS and Markdown.
- ***Easy Comments*** - Setting up commenting was as easy as adding my Disqus short name to the config.yml file.
- ***Quick Code Snippets*** - Octopress includes a code block plugin that includes language specific syntax highlighting.

My hope is that with all of this combined, I'll write more.  I really do enjoy writing about tech topics as I think it helps me solidify my knowledge and hopefully others will find the content useful.