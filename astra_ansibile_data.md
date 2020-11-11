---
layout: default
title: Astra + Ansible
description: Astra Data API with Ansible
permalink: /astra/ansible/data/
---

# Asta Data API

The Astra Data API allows Astra users to take database level actions against tables, rows, and columns.  In order to use the Data API you will need some info from the DevOps API or the Astra UI.  You will need <span style="color: #d14;">[cluster_id]</span>, <span style="color: #d14;">[cluster_region]</span>, and the username and password for your database.

## Data Auth Post

Add your <span style="color: #d14;">[cluster_id]</span>, <span style="color: #d14;">[cluster_region]</span>, username and password then run the playbook and grab the authToken from the response.  You will use this token for <span style="color: #d14;">[token from data_auth_post]</span> in all playbook demos below.

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_rest_api_url: "https://[cluster_id]-[cluster_region].apps.astra.datastax.com/api/rest"
    astra_auth_request_body:
      username: "username"
      password: "password"
  tasks:
    - name: create auth
      uri:
        url: "{{ astra_rest_api_url }}/v1/auth"
        method: POST
        headers:
          Content-Type: "application/json"
        body: "{{ astra_auth_request_body | to_json }}"
        status_code: 201
      register: request_auth
    - name: debug
      debug:
        var: request_auth
```
{% endraw %}

## Data Get Keyspaces

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_rest_api_url: "https://[cluster_id]-[cluster_region].apps.astra.datastax.com/api/rest"
    astra_auth_token: "[token from data_auth_post]"
  tasks:
    - name: get keyspaces
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces"
        method: GET
        headers:
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        status_code: 200
      register: response_keyspaces
    - name: debug
      debug:
        var: response_keyspaces
```
{% endraw %}

## Data Create Table

Creating a table requires the <span style="color: #d14;">[keyspace for table]</span> as well as the schema level information required for the table structure columns, key, and partition.  In the demo below I create a basic table just to show functionality.  Please refer to documentation for complete capability for complicated data models.  

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_rest_api_url: "https://[cluster_id]-[cluster_region].apps.astra.datastax.com/api/rest"
    astra_auth_token: "[token from data_auth_post]"
    astra_keyspace: "[keyspace for table]"
    astra_create_table: 
      name: "table_name"
      columnDefinitions:
        - 
          name: "id"
          typeDefinition: "text"
        - 
          name: "value"
          typeDefinition: "text"
      primaryKey: 
        partitionKey:
          - id           
  tasks:
    - name: create table
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables"
        method: POST
        headers:
          Content-Type: "application/json"
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        body: "{{ astra_create_table | to_json }}"
        status_code: 201
      register: response_table
    - name: debug
      debug:
        var: response_table
```
{% endraw %}

## Data Get Tables

{% raw %}
```js
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_rest_api_url: "https://[cluster_id]-[cluster_region].apps.astra.datastax.com/api/rest"
    astra_auth_token: "[token from data_auth_post]"
    astra_keyspace: "[keyspace for table]"
  tasks:
    - name: get tables
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables"
        method: GET
        headers:
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        status_code: 200
      register: response_tables
    - name: debug
      debug:
        var: response_tables
```
{% endraw %}

## Data Get Table

{% raw %}
```js
 
```
{% endraw %}

## Data Delete Table

{% raw %}
```js
 
```
{% endraw %}

## Data Add Rows

{% raw %}
```js
 
```
{% endraw %}

## Data Submit Queries

{% raw %}
```js
 
```
{% endraw %}

## Data Retrieve Rows

{% raw %}
```js
 
```
{% endraw %}

## Data Update Rows

{% raw %}
```js
 
```
{% endraw %}

## Data Delete Rows

{% raw %}
```js
 
```
{% endraw %}

## Data Retrieve Columns

{% raw %}
```js
 
```
{% endraw %}

## Data Add Column

{% raw %}
```js
 
```
{% endraw %}

## Data Retrieve Column

{% raw %}
```js
 
```
{% endraw %}

## Data Update Column

{% raw %}
```js
 
```
{% endraw %}

## Data Delete Column
