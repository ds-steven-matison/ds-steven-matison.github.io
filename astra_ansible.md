---
layout: default
title: Astra + Ansible
description: How to leverage Astra with Ansible
permalink: /astra/ansible/
---

# What is Ansible?

Ansible is an open-source software provisioning, configuration management, and application-deployment tool enabling infrastructure as code. It runs on many Unix-like systems, and can configure both Unix-like systems as well as Microsoft Windows.

# How do I use Ansible?

I am a mac user with homebrew already installed so installing ansible is as simple as:

<b>></b>```brew install ansible```

Next executing ansible playbooks is as easy as:

<b>></b>```ansible-playbook [playbook]```

# Check Out My GitHub Repo

Before getting started with Astra & Ansible download, fork, or clone my github repo which contains sample ansible playbooks to give you a head start.  For the sake of demonstrating each action separately each ability is a separate playbook but you can easily code them into a single playbook to suit your needs.  
<div class="mui-container">
    <div class="home mui-row">

      <div class="mui-col-md-6">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="https://github.com/ds-steven-matison/astra-ansibile-demo">GitHub: Astra Ansible Demo</a>
            </span>
            <div class="img-overlay"></div>
            <a href="https://github.com/ds-steven-matison/astra-ansibile-demo" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">My GitHub repo which contains example ansible playbooks for the Astra DevOps and Data APIs.</p>
            GitHub Repo: <a href="https://github.com/ds-steven-matison/astra-ansibile-demo">astra-ansible-demo</a>
          </div>
        </article>
    </div>
  </div>
</div>

# Working with Ansible Playbooks

There are 2 different Astra APIs.  The DevOps API for creating databases, keyspaces, and taking organizational actions.  Second, the Data API for creating tables, data, and taking adminstrative actions on databases created with the DevOps API.

<div class="mui-container">
  <div class="home mui-row">

      <div class="mui-col-md-3">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/astra/ansible/devops/">Astra DevOps API</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/astra/ansible/devops/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">Learn how to use the DevOps API.</p>
          </div>
        </article>
      </div>

      <div class="mui-col-md-3">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/astra/ansible/data/">Astra Data API</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/astra/ansible/data/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">Learn how to use the Data API.</p>
          </div>
        </article>
      </div>
  </div>
</div>

{% include astra_help.html %}