---
layout: default
title: Astra Perseverance
description: How to Astra Perseverance
permalink: /astra/perseverance/
---

# How To: Evaluate DSE Cassandra Clusters for Astra Migration

One of my co-works [ssdatastax](https://github.com/ssdatastax)recently built a python tool called Astra Perseverance which executes against a diagnostic tarball from DSE Opscenter and provides some information needed for astra sizing.   You can find the tool [here](https://github.com/ssdatastax/astra-perseverance#getting-started).  In this short how to I am going to show you how to setup your macbook environment and execute this script.

First setup your environment:

```js
brew install python3
pip3 install pandas 
pip3 install xlsxwriter
```
Next install the tool:
```js
git clone https://github.com/ssdatastax/astra-perseverance.git
```
Now execute the tool:
```js
cd astra-perseverance
python3 explore.py -p /Users/stevenmatison/Downloads/test/Cluster_1 -p /Users/stevenmatison/Downloads/test/Cluster_2 -p /Users/stevenmatison/Downloads/test/Cluster_3
```

After execution an Excel Spreadsheet will be located in each folder.

:warning: Notice I use python3 and pip3 here instead of documented python and pip.  I had issues with existing python2 install not taking pandas or xlsxwriter.  

{% include astra_help.html %}