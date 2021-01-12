---
layout: default
title: GitHub Page Readme
description: How to create a custom github readme summary
permalink: /blog/github/page/readme/
---

# How To: Create a GitHub Page Readme

Back in the day before search engines, when you wanted to make a website you had to go to someone else's website, view source, and take it.  In this typical hacker fashion I have learned how to make a GitHub Page Summary using a readme file.

## Let's Get Started

Credit where credit is due:  I found this original snippet from [Clun](https://github.com/clun).   All I had to do was copy the source:

```js
### Hi This is Cedrick Lunven, Director of Developer Advocacy @DataStax

- 🔭 I’m currently working on `FF4j` [ff4j.org](ff4j.org) and DataStax Sample applications
- 🌱 I’m currently learning `React`, `Redux` and more `Javascript`
- 💬 Ask me about `FF4j`, `NoSQL` databases
- 📫 How to reach me: [@clunven](https://twitter.com/clunven) on twitter
- ⚡ **Fun fact:** *everything has an end except a sausage that has two ends*
```

Next I create the base [repo](https://github.com/ds-steven-matison/ds-steven-matison) (same repo name as my username) in my account, add the README.md source, and modify to suit my needs:

```js
### Hi This is Steven Matison, Presales Data Architect @DataStax

- 🔭 I’m currently working on Astra, NoSqlBench, & Stargate
- 🌱 I’m currently learning Kubernetes, DSE Spark, DSE Graph 
- 💬 Ask me about Astra, Cassandra, Stargate, Open Source, NiFi, Apache Software Foundation, Apis
- 👯 I’m looking to collaborate on NoSqlBench, NiFi, OpenSource 
- 📫 How to reach me: [@DSMatison](https://twitter.com/DSMatison) on twitter
- ⚡ Check out my [GitHub Pages Datastax Journal](https://ds-steven-matison.github.io/)
```

Next I did this on my previous github so anyone finding me [there](https://github.com/steven-matison) can see all my current stuff [here](https://github.com/ds-steven-matison).  Ty Cedric!!

## Adding a Summary Page Counter

While crusing the internet today I ran into another Git Hub Users [Tusharojha](https://github.com/tusharojha) who had a page counter in his summary.   I quickly viewed source, grabbed the code, modified my username, and added to README.md:

```js
<img src="https://komarev.com/ghpvc/?username=ds-steven-matison" alt="ds-steven-matison" />
```

and viola it is now added it to my page.  Ty Tushar!!

## What's Next?

Check out [How To: Add Jekyll Sitemap Generator](/blog/jekyll/sitemap/).  Be sure to reach out to me to share your blog or if you have any questions, issues, or feedback from creating your GitHub Jekyll Theme Page!!  