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
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    astra_rest_api_url: "https://[cluster_id]-[cluster_region].apps.astra.datastax.com/api/rest"
    astra_auth_token: "[token from data_auth_post]"
    astra_keyspace: "[keyspace for table]"
    astra_table: "[table]"
  tasks:
    - name: get table
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces//{{ astra_keyspace }}/tables/{{ astra_table }}"
        method: GET
        headers:
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        status_code: 200
      register: response_table
    - name: debug
      debug:
        var: response_table
```
{% endraw %}

## Data Delete Table

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
    astra_table: "[table]"
  tasks:
    - name: delete table
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces//{{ astra_keyspace }}/tables/{{ astra_table }}"
        method: DELETE
        headers:
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        status_code: 204
      register: response_delete
    - name: debug
      debug:
        var: response_delete
```
{% endraw %}

## Data Add Rows

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
    astra_table: "[table]"
    astra_add_row:
      columns:
        - name: "id"
          value: 1
        - name: "value"
          value: "some text"
  tasks:
    - name: add rows
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables/{{ astra_table }}/rows"
        method: POST
        headers:
          Content-Type: "application/json"
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        body: "{{ astra_add_row | to_json }}"
        status_code: 201
      register: response_rows
    - name: debug
      debug:
        var: response_rows
```
{% endraw %}

## Data Submit Queries

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
    astra_table: "[table]"
    astra_query:
      columnNames:
        - "id"
        - "value"
      filters:
        -
          value:
            - "1"
          columnName: "id"
          operator: "eq"
  tasks:
    - name: submit query
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables/{{ astra_table }}/rows/query"
        method: POST
        headers:
          Content-Type: "application/json"
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        body: "{{ astra_query | to_json }}"
        status_code: 200
      register: response_query
    - name: debug
      debug:
        var: response_query
```
{% endraw %}

## Data Get Rows

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
    astra_table: "[table]"
  tasks:
    - name: get rows
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables/{{ astra_table }}/rows"
        method: GET
        headers:
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        status_code: 200
      register: response_rows
    - name: debug
      debug:
        var: response_rows
```
{% endraw %}

## Data Get Row

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
    astra_table: "[table]"
    astra_row: "[primary Key for row]"
  tasks:
    - name: get row
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables/{{ astra_table }}/rows/{{ astra_row }}"
        method: GET
        headers:
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        status_code: 200
      register: response_row
    - name: debug
      debug:
        var: response_row
```
{% endraw %}

## Data Update Rows

A quick warning here: you cannot update the Primary Key.
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
    astra_table: "[table]"
    astra_row: 1
    astra_update_row:
      changeset:
        - column: "value"
          value: "some updated text"
  tasks:
    - name: update rows
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables/{{ astra_table }}/rows/{{ astra_row }}"
        method: PUT
        headers:
          Content-Type: "application/json"
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        body: "{{ astra_update_row | to_json }}"
        status_code: 200
      register: response_update
    - name: debug
      debug:
        var: response_update
```
{% endraw %}

## Data Delete Rows

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
    astra_table: "[table]"
    astra_row: 1
  tasks:
    - name: delete rows
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables/{{ astra_table }}/rows/{{ astra_row }}"
        method: DELETE
        headers:
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        status_code: 204
      register: response_delete
    - name: debug
      debug:
        var: response_delete
```
{% endraw %}

## Data Get Columns

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
    astra_table: "[table]"
  tasks:
    - name: get columns
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables/{{ astra_table }}/columns"
        method: GET
        headers:
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        status_code: 200
      register: response_columns
    - name: debug
      debug:
        var: response_columns
```
{% endraw %}

## Data Get Column

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
    astra_table: "[table]"
    astra_column: "value"
  tasks:
    - name: get column
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables/{{ astra_table }}/columns/{{ astra_column }}"
        method: GET
        headers:
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        status_code: 200
      register: response_column
    - name: debug
      debug:
        var: response_column
```
{% endraw %}

## Data Add Column

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
    astra_table: "[table]"
    astra_add_column:
      static: false
      name: "newColumn"
      typeDefinition: "text"
  tasks:
    - name: add column
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables/{{ astra_table }}/columns"
        method: POST
        headers:
          Content-Type: "application/json"
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        body: "{{ astra_add_column | to_json }}"
        status_code: 201
      register: response_column
    - name: debug
      debug:
        var: response_column
```
{% endraw %}

## Data Update Column

A quick warning here: you can only update the Primary Key.
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
    astra_table: "[table]"
    astra_column: "id"
    astra_update_column:
      newName: "updatedid"
  tasks:
    - name: update column
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables/{{ astra_table }}/columns/{{ astra_column }}"
        method: PUT
        headers:
          Content-Type: "application/json"
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        body: "{{ astra_update_column | to_json }}"
        status_code: 200
      register: response_column
    - name: debug
      debug:
        var: response_column
```
{% endraw %}

## Data Delete Column

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
    astra_table: "[table]"
    astra_column: "newColumn"
  tasks:
    - name: delete column
      uri:
        url: "{{ astra_rest_api_url }}/v1/keyspaces/{{ astra_keyspace }}/tables/{{ astra_table }}/columns/{{ astra_column }}"
        method: DELETE
        headers:
          Accept: "application/json"
          X-Cassandra-Token: "{{ astra_auth_token }}"
        status_code: 204
      register: response_column
    - name: debug
      debug:
        var: response_column
```
{% endraw %}

{% include astra_help.html %}