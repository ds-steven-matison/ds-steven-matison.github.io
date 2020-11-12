---
layout: default
title: Astra + Ansible
description: Astra DevOps API with Ansible
permalink: /astra/ansible/devops/
---

# Asta DevOps API

The Astra DevOps API allows Astra users to create databases, keyspaces, and take other DevOps actions at an organization level.  You will need to login to Astra UI, select your organization, and create a Service Account.  Once you create a Service Account click into menu on right side and choose Copy Credentials.  You should end up with a json object of the values you need.  You can now leave the Astra UI behind and work entirely from within ansible.

## DevOps Auth Post

Add your Service Account details to astra_auth_request_body, run the playbook, and grab the JWT token from the response.  You will use this token for <span style="color: #d14;">[token from devops_auth_post]</span> in all playbook demos below.

{% raw %}
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
{% endraw %}

## DevOps Create Database

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_api_url: "https://api.astra.datastax.com/v2/databases"
    astra_auth_token: "[token from devops_auth_post]"
    astra_create_table:
      name: "table_name"
      keyspace: "keyspace_name"
      cloudProvider: "gcp"
      tier: "developer"
      capacityUnits: 1
      region: "us-east1"
      user: "username"
      password: "password"
  tasks:
    - name: create database
      uri:
        url: "{{ astra_api_url }}"
        method: POST
        headers:
          Accept: "application/json"
          Content-Type: "application/json"
          Authorization: "Bearer {{ astra_auth_token }}"
        body: "{{ astra_create_table | to_json }}"
        status_code: 201
      register: response_database
    - name: debug
      debug:
        var: response_database
```
{% endraw %}
## DevOps Get Databases

In order to leverage api actions against a database, you need the database's Cluster ID.  You can find this in the Astra UI or you can use this playbook to get the id.  You will need this ID for all <span style="color: #d14;">[astra database id]</span> instances in further playbooks.

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_api_url: "https://api.astra.datastax.com/v2/databases"
    astra_auth_token: "[token from devops_auth_post]"
  tasks:
    - name: get databases
      uri:
        url: "{{ astra_api_url }}"
        method: GET
        headers:
          Accept: "application/json"
          Content-Type: "application/json"
          Authorization: "Bearer {{ astra_auth_token }}"
        status_code: 200
      register: response_databases
    - name: debug
      debug:
        var: response_databases
```
{% endraw %}
## DevOps Add Keyspace to Database

When you create a database above, the keyspace above will be automatically created.  This playbook allows you to add additional keyspaces.

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_api_url: "https://api.astra.datastax.com/v2/databases/[cluster_id]/keyspaces/newKeyspace"
    astra_auth_token: "[token from devops_auth_post]"
  tasks:
    - name: add keyspace
      uri:
        url: "{{ astra_api_url }}"
        method: POST
        headers:
          Accept: "application/json"
          Content-Type: "application/json"
          Authorization: "Bearer {{ astra_auth_token }}"
        status_code: 201
      register: response_keyspace
    - name: debug
      debug:
        var: response_keyspace
```
{% endraw %}
## DevOps Reset Database Password

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_api_url: "https://api.astra.datastax.com/v2/databases"
    astra_auth_token: "[token from devops_auth_post]"
    astra_database_id: "[astra database id]"
    astra_user_password:
      username: "username"
      password: "password"
  tasks:
    - name: reset password
      uri:
        url: "{{ astra_api_url }}/{{ astra_database_id }}/resetPassword"
        method: POST
        headers:
          Accept: "application/json"
          Content-Type: "application/json"
          Authorization: "Bearer {{ astra_auth_token }}"
        body: "{{ astra_user_password | to_json }}"
        status_code: 202
      register: response_password
    - name: debug
      debug:
        var: response_password
```
{% endraw %}
## DevOps Resize Database

{% raw %}
```js

```
{% endraw %}
## DevOps Park Database

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_api_url: "https://api.astra.datastax.com/v2/databases"
    astra_auth_token: "[token from devops_auth_post]"
    astra_database_id: "[astra database id]"
  tasks:
    - name: park database
      uri:
        url: "{{ astra_api_url }}/{{ astra_database_id }}/park"
        method: POST
        headers:
          Accept: "application/json"
          Content-Type: "application/json"
          Authorization: "Bearer {{ astra_auth_token }}"
        body: ""
        status_code: 202
      register: response_park
    - name: debug
      debug:
        var: response_park
```
{% endraw %}
## DevOps Unpark Database

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_api_url: "https://api.astra.datastax.com/v2/databases"
    astra_auth_token: "[token from devops_auth_post]"
    astra_database_id: "[astra database id]"
  tasks:
    - name: unpark database
      uri:
        url: "{{ astra_api_url }}/{{ astra_database_id }}/unpark"
        method: POST
        headers:
          Accept: "application/json"
          Content-Type: "application/json"
          Authorization: "Bearer {{ astra_auth_token }}"
        body: ""
        status_code: 202
      register: response_unpark
    - name: debug
      debug:
        var: response_unpark
```
{% endraw %}
## DevOps Terminate Database

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_api_url: "https://api.astra.datastax.com/v2/databases"
    astra_auth_token: "[token from devops_auth_post]"
    astra_database_id: "[astra database id]"
  tasks:
    - name: terminate database
      uri:
        url: "{{ astra_api_url }}/{{ astra_database_id }}/terminate"
        method: POST
        headers:
          Accept: "application/json"
          Content-Type: "application/json"
          Authorization: "Bearer {{ astra_auth_token }}"
        body: ""
        status_code: 202
      register: response_terminate
    - name: debug
      debug:
        var: response_terminate
```
{% endraw %}
## DevOps Get Secure Bundle URL

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_api_url: "https://api.astra.datastax.com/v2/databases"
    astra_auth_token: "[token from devops_auth_post]"
    astra_database_id: "[astra database id]"
  tasks:
    - name: get bundle
      uri:
        url: "{{ astra_api_url }}/{{ astra_database_id }}/secureBundleURL"
        method: POST
        headers:
          Accept: "application/json"
          Content-Type: "application/json"
          Authorization: "Bearer {{ astra_auth_token }}"
        body: ""
        status_code: 200
      register: response_bundle
    - name: debug
      debug:
        var: response_bundle
```
{% endraw %}
## DevOps Get Supported Regions

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_api_url: "https://api.astra.datastax.com/v2/availableRegions"
    astra_auth_token: "[token from devops_auth_post]"
  tasks:
    - name: return regions
      uri:
        url: "{{ astra_api_url }}"
        method: GET
        headers:
          Accept: "application/json"
          Content-Type: "application/json"
          Authorization: "Bearer {{ astra_auth_token }}"
        body: ""
        status_code: 200
      register: response_regions
    - name: debug
      debug:
        var: response_regions
```
{% endraw %}