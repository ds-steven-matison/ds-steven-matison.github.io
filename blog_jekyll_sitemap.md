---
layout: default
title: Jekyll Sitemap
description: Add Jekyll Sitemap Generator
permalink: /blog/jekyll/sitemap/
---

# How To: Add Jekyll Sitemap Generator
In my previous blog [How To: Create A GitHub.io Page](/blog/create/github/page/) I showed you how to great your GitHub.io page.   In this blog i am going to show you how to use a [Jekyll Sitemap Generator](https://github.com/jekyll/jekyll-sitemap) to add a sitemap to your page.   I am using [Google Analytics](http://analytics.google.com/) and [Google Search](https://search.google.com/search-console) to monitor traffic and SEO results.  Since google wants a sitemap and I do not want to manage it, automating this part of the blog is now needed.  

# Update config.yml

<p>&nbsp;</p>
Just add:
```js
plugins:
  - jekyll-sitemap
```

# Update Gemfile

<p>&nbsp;</p>
Just add:
```js
gem 'jekyll-sitemap'
```

# Run Bundle

In your root folder execute the command:
```js
bundle
bundle exec jekyll serve
```

# Exclude Pages

If you want to exclude pages from your sitemap use this syntax in the front matter for your page:
```js
sitemap: false
```

# Confirm Sitemap Exists:

You should see a new file sitemap.xml in your local /site/ folder.  And this url should now work:

  [http://localhost:4000/sitemap.xml](http://localhost:4000/sitemap.xml)

After commit your changes to your repo be sure to check your github.io page.  For example my site map is now:

[https://ds-steven-matison.github.io/sitemap.xml](https://ds-steven-matison.github.io/sitemap.xml)

## What's Next?

Check out [How To: Create a GitHub Page Readme](/blog/github/page/readme/).  Be sure to reach out to me to share your blog or if you have any questions, issues, or feedback from creating your GitHub Jekyll Theme Page!! 