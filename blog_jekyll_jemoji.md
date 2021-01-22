---
layout: default
title: Jekyll Jemoji
description: Use Emojis in your Jekyll Theme
permalink: /blog/jekyll/jemoji/
---

# How To: Add Emoticons to Jekyll Theme
In my previous blog [How To: Add Jekyll Sitemap Generator](/blog/jekyll/sitemap/) I showed you how to add a sitemap your GitHub.io page.   In this blog i am going to show you how to use a [Jemoji](https://github.com/jekyll/jemoji) to add markdown for emoji icons in you jekyll theme.   Everyone uses icons now in chat and text so why not in my blog too?

# Update config.yml

<p>&nbsp;</p>
Just add:
```js
plugins:
  - jemoji
```

# Update Gemfile

<p>&nbsp;</p>
Just add:

```js
gem 'jemoji'
```

# Run Bundle

In your root folder execute the command:
```js
bundle
bundle exec jekyll serve
```

# Test the Markdown

```js
I give this plugin two :+1:!
```
I give this plugin two :+1:!

You can find the full of Emojis [here](/blog/jekyll/jemoji/list/).  Copy the one you want, and paste it into markdown!

# Customizing Emoji Images Locally

By default the emojis are github cdn provided,  to host your own images just add this to your _config.yml file:

```js
emoji:
  src: "/assets/images/emoji"
```

Of course be sure to download all the emojis.  You can find more details [here](https://github.com/github/gemoji).

## What's Next?

Check out [How To: Create a GitHub Page Readme](/blog/github/page/readme/).  Be sure to reach out to me to share your blog or if you have any questions, issues, or feedback from creating your GitHub Jekyll Theme Page!! 