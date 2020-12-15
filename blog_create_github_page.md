---
layout: default
title: GitHub Page
description: Create Your Own GitHub.io Page
permalink: /blog/create/github/page/
---

# How To: Create a GitHub.io Page
I have made tons of websites, in many different website frameworks.  I have made many repositories and used github repos to hold website applications for manual and automated deployments.  When I set out to create a blog for my experiences and projects at Datastax I found [GitHub Pages](https://pages.github.com/).  This is by far the easiest way to role out a webpage with no concern to traditional friction to creating a "website".

## Let's Get Started

First create a repo on your GitHub in the format of your "username.github.io".  For example my repo is: [ds-steven-matison.github.io](https://github.com/ds-steven-matison/ds-steven-matison.github.io). Next, clone your repo, add your website index.html, commit, and visit your GitHub Page by visiting your custom url.  Your repo will now automatically update your website upon future commits.  Amazing: no need to sync website to webserver.  No FTP!!! No need to manage IPs, DNS, or SSL.  Thank you GitHub!!

## Using Jekyll With GitHub Pages

For my blog I chose to use Jekyll and a Jekyll theme: [Architect](https://jekyllthemes.io/theme/architect-theme).   You can find more themes [here](https://jekyllthemes.io/github-pages-themes).   To use the jekyll them, download it, add to your repo, commit, and check your blog to confirm its operational with the base theme.  Next, begin to modify your pages to suit your personal style.

## Some Very Cool Jekyll Features

1.  Your main site template is in /layouts/default.html.  You can update your main site framework in this file.
2.  All of your pages are defined in the header of the .md file.  Check out template.md for markdown examples.
3.  You can work locally on your blog with the url http://localhost:4000 by executing: <b><i>bundle exec jekyll serve</i></b> in your root repo folder.  Use this while doing rapid page building and editing cycles.
4.  Updates to markdown (.md) files will automatically update your localhost version of the site.  Just save, and refresh.  When done commit, and viola your GitHub Page is auto updated just a few moments later.
5.  Html includes are possible using folder /includes/ and following syntax: <b><i>{ % include content.html % }</i></b>. Just be sure to remove spaces between %s and {}s.  I had to add that so it would not include in this page.


## What's Next?

Check out [How To: Add Jekyll Sitemap Generator](/blog/jekyll/sitemap/).  Be sure to reach out to me to share your blog or if you have any questions, issues, or feedback from creating your GitHub Jekyll Theme Page!!  