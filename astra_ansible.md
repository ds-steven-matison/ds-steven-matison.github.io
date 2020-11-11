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

# Working with Ansibile Playbooks

Before getting started with Astra & Ansible download, fork, or clone my github repo which contains sample ansible playbooks to give you a head start
<div class="mui-container">
    <div class="home mui-row">

      <div class="mui-col-md-6">
        <article class="mui-panel">
          <div class="bg-holder bg-deep-purple" style="background:url(/assets/images/database.jpg) no-repeat center center">
            <span class="post-list-title">
              <a class="post-link" href="/astra/postman/">GitHub: Astra Ansible Demo</a>
            </span>
            <div class="img-overlay"></div>
            <a href="/astra/postman/" class="overlay"></a>
          </div>
          <div class="post-data">
            <p class="post-excerpt">My GitHub repo which contains example ansible playbooks for the Astra DevOps and Data APIs.</p>
            GitHub Repo: <a href="https://github.com/ds-steven-matison/astra-ansibile-demo">astra-ansible-demo</a>
          </div>
        </article>
    </div>

  </div>
</div>

# Asta DevOps API

The Astra DevOps API allows Astra users to create databases, keyspaces, and take other DevOps actions at an organization level.  You will need to login to Astra UI, select your organization, and create a Service Account.  Once you create a Service Account click into menu on right side and choose Copy Credentials.  You should end up with a json object of the values you need.  You can now leave the Astra UI behind and work entirely from within ansible.

## DevOps Auth Post

Add your Service Account details to astra_auth_request_body.  Run the playbook and grab the JWT token from the response.  
```js
---

- hosts: localhost
  connection: local
  gather_facts: no

  vars:
    astra_api_url: "https://api.astra.datastax.com/v2/authenticateServiceAccount"
    astra_auth_request_body:
      clientName: "[asta_service_account_clientName]"
      clientId: "[astra_service_account_clientId]"
      clientSecret: "[astra_service_account_clientSecret]"

  tasks:
    - name: create auth
      uri:
        url: "{{ astra_api_url }}"
        method: POST
        headers:
          Accept: "application/json"
          Content-Type: "application/json"
        body: "{{ astra_auth_request_body | to_json }}"
        status_code: 200
      register: response_auth

    - name: debug
      debug:
        var: response_auth
```

## DevOps Create Database

## DevOps Get Databases

## DevOps Add Keyspace to Database

## DevOps Reset Database Password

## DevOps Resize Database

## DevOps Park Database

## DevOps Unpark Database

## DevOps Terminate Database

## DevOps Get Secure Bundle URL

## DevOps Get Supported Regions

