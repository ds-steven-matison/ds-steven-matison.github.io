---
layout: default
title: NoSQLBench Contribution 2
description: Working on NoSqlBench Documentation
permalink: /nosqlbench/contribution/2/
---

# Contributing to NoSQLBench Documentation

When I first got started with nosqlbench I spent some time going through the [Github Issues](https://github.com/nosqlbench/nosqlbench/issues).  I find this a great way to get started with any project contribution.  It is also a great way to get an understanding of current issues and areas already identified for improvement.   I was able to quickly take note several issues I could have an impact on in the future.   

One such issue I personally ran into while working on my blog post [How To: NoSqlBench with Astra](/nosqlbench/astra/) was [GitHub Issue #198](https://github.com/nosqlbench/nosqlbench/issues/198).  While trying to connect nosqlbench to astra I had to infer settings based on command line help doc reference to "securebundle" but there is some work to do there too.   I got the info I needed from a co-worker but it also happened to already be in #198.

I quickly added the Astra example to current cql document page ([commit](https://github.com/ds-steven-matison/nosqlbench/commit/909e7609ffdd20fa6a139207d545a7e89a949632)) and made a quick comment on the issue.   I am not sure this is the right place for that content, and maybe a separate Astra page is going to be more appropriate.  More here later once I get feedback.

# NoSQLBench Slack Community

While monitoring [NoSqlBench Slack](https://nosqlbench.slack.com/) a member was looking for command line settings for SSL.  I knew I have seen these in my short time here on the project, but I was able to confirm the users experience:  404.   Oops, thats pretty brutal.  A quick dig and I was able to find some references internally but nothing in the doc.   Another co-worker quickly responded with following command topic:

```js
java -jar help ssl
````

# Solving Documentation Issues

1.  Nothing in command line help topics to indicate we can execute ssl command above:  Issue 237
2.	The link in cql.md to deeper ssl documentation was a 404: Issue 238
3.  Other examples(6) across the code base were a 404 too: Issue 238


## GitHub Issue #237

To begin to solve #1 I create a new [GitHub Issue #237](https://github.com/nosqlbench/nosqlbench/issues/237).  An issue is a great way to start discussion with other contributors.  In this case my [commit](https://github.com/ds-steven-matison/nosqlbench/commit/bf51056fce58b7b8ee540d0490d1db1d16c5f49c) was easy, but I want to keep the discussion open and not execute a PR until I can get feedback from the community about other help topics I can add at the same time.  On my local fork I built the project again and confirm this new ssl topic is now showing up:

```js
java -jar nb.jar help topics         
# global help topics
- commandline
- cli-scripting
- ssl
```

Update 12/17/2020: It took me some time, but I was able to get the above change and another [Issue #198](https://github.com/nosqlbench/nosqlbench/issues/198) into a [PR #243](https://github.com/nosqlbench/nosqlbench/pull/243).  My first contribution to documentation is now complete and I am able to close 2 issues.

### GitHub Issue #238

I had already created [GitHub Issue #238](https://github.com/nosqlbench/nosqlbench/issues/238) even before I got started working on documentation improvements.  To solve #2 & #3 I dig into the source code and locate all examples of: "Additional parameters may need to be provided".  This results in multiple instances that look like this:

```js
[Additional parameters may need to be provided](ssl.md).
[Additional parameters may need to be provided](../../../../driver-cql/src/main/resources/ssl.md).
etc
````

In the first example, html link is rendered as: http://docs.nosqlbench.io/services/docs/drivers/ssl.mdundefined with an undefined appended on the end.  That should be rendered as: http://docs.nosqlbench.io/#/docs/drivers/ssl.md, but for some reason the latter is still a 404.  If I look at the [parent page](http://docs.nosqlbench.io/#/docs/drivers/cql.md) the ssl doc page should actually be:  http://docs.nosqlbench.io/#/docs/drivers/ssl.md.  Oddly enough this is a 404 too.

In the next one, and others similar to it, html link is rendered as: http://docs.nosqlbench.io/driver-cql/src/main/resources/ssl.md
but should be rendered as "/docs/drivers/ssl.md".  The code base relative path and the URL path are quite different here.  I am also questioning the # in the URL.   If I can get rendering issues sorted out I should be able to change all instances to:

```js
[Additional parameters may need to be provided](/docs/drivers/ssl.md).
```

This concludes my time for today on this topic, but I will be sure to come back and update once I have had time to discuss each issue internally, make further improvements, and get closer to a PR.   

{% include nosqlbench_help.html %}