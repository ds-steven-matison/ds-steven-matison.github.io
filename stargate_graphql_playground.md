---
layout: default
title: GraphQL Playground
description: A blog all about GraphQL Playground
permalink: /stargate/graphql/playground/
---

# What is GraphQl Playground?

Bundled within the docker stargate is an instance of GraphQL Playground.  This UI provides an interface to execute GraphQL queries against Stargate & Astra APIs.

# Working with GraphQL Playground

With Stargate Docker running you can access GraphQL Playground with the following Url:

[http://localhost:8080/playground](http://localhost:8080/playground)

Here are few things I learned:
*	Your changes in this UI will not persist.  If you want to save your work, see [ How To: Import/Export GraphQl Playground Tabs](#howto1)
*	Check out HTTP HEADERS (bottom right), you'll need your auth token, and also notice the case change here from "X-Cassandra-Token" to "x-cassandra-token".
*	When working on calls, check out the Copy Curl button.  This will expose the exact curl call you are executing under the hood.  I have used this extensively.
*	A new tab will not have your auth token and you will need to adjust the url


# GraphQL Posts

As I create useful markup here, I will move it into specific posts.  

<a name="howto1"></a>
## How To: Import/Export GraphQl Playground Tabs

My first experience with GraphQL Playground went pretty well, but I quickly ran into some issues once i realized that the tabs do not persist.    I had went through documentation and filled my screen with tabs.   Next time I game back to GraphQL they were all gone.  Oops.   This behavior really pushes hard for an import/export feature.  A quick google search later I wound up on an [Open Github Issue #6](https://github.com/graphql/graphql-playground/issues/6). In the comments a user exposes a method to import/export the tabs.  I figured this would be a great first post for my GraphQL Playground blog.

First I install the Google Chome Extension for [Local Storage Handler](https://chrome.google.com/webstore/detail/localstorage-manager/fkhoimdhngkiicbjobkinobjkoefhkap/related) and download the graphql-playground object.  It is a huge minimized json object but you can click the popup button to make it more human readable:

```js
❮
{
    "workspaces": {
        "": {
            "docs": {
                "ckhmat9ju00003h5oum6jf1mf": {
                    "navStack": [],
                    "docsOpen": false,
                    "docsWidth": 300,
                    "activeTabIdx": null,
                    "keyMove": false
                }
            },
            "sessions": {
                "sessions": {
                    "ckhmat9ju00003h5oum6jf1mf": {
                        "id": "ckhmat9ju00003h5oum6jf1mf",
                        "query": "# Write your query or mutation here\n",
                        "variables": "",
                        "responses": [],
                        "endpoint": "",
                        "hasMutation": false,
                        "hasSubscription": false,
                        "hasQuery": false,
                        "queryTypes": {
                            "firstOperationName": null,
                            "subscription": false,
                            "query": false,
                            "mutation": false
                        },
                        "subscriptionActive": false,
                        "date": "2020-11-17T18:16:39.066Z",
                        "starred": false,
                        "queryRunning": false,
                        "operations": [],
                        "isReloadingSchema": false,
                        "isSchemaPendingUpdate": false,
                        "responseExtensions": {},
                        "queryVariablesActive": false,
                        "endpointUnreachable": false,
                        "editorFlex": 1,
                        "variableEditorOpen": false,
                        "variableEditorHeight": 200,
                        "responseTracingOpen": false,
                        "responseTracingHeight": 300,
                        "docExplorerWidth": 350,
                        "variableToType": {},
                        "headers": "",
                        "isFile": false
                    }
                },
                "selectedSessionId": "ckhmat9ju00003h5oum6jf1mf",
                "sessionCount": 1,
                "headers": ""
            },
            "sharing": {
                "history": false,
                "headers": true,
                "allTabs": true,
                "shareUrl": null
            },
            "history": {}
        },
        "/graphql-schema": {
            "docs": {
                "ckhmat9ju00003h5oum6jf1mf": {
                    "navStack": [],
                    "docsOpen": false,
                    "docsWidth": 300,
                    "activeTabIdx": null,
                    "keyMove": false
                },
                "ckhmat9oe00013h5o792k7kqp": {
                    "navStack": [],
                    "docsOpen": false,
                    "docsWidth": 300,
                    "activeTabIdx": null,
                    "keyMove": false
                }
            },
            "sessions": {
                "sessions": {
                    "ckhmat9oe00013h5o792k7kqp": {
                        "id": "ckhmat9oe00013h5o792k7kqp",
                        "query": "{ keyspaces{ name } }",
                        "variables": "",
                        "responses": [],
                        "endpoint": "/graphql-schema",
                        "hasMutation": false,
                        "hasSubscription": false,
                        "hasQuery": false,
                        "queryTypes": {
                            "firstOperationName": null,
                            "subscription": false,
                            "query": false,
                            "mutation": false
                        },
                        "subscriptionActive": false,
                        "date": "2020-11-17T18:16:39.066Z",
                        "starred": false,
                        "queryRunning": false,
                        "operations": [],
                        "isReloadingSchema": false,
                        "isSchemaPendingUpdate": false,
                        "responseExtensions": {},
                        "queryVariablesActive": false,
                        "endpointUnreachable": false,
                        "editorFlex": 1,
                        "variableEditorOpen": false,
                        "variableEditorHeight": 200,
                        "responseTracingOpen": false,
                        "responseTracingHeight": 300,
                        "docExplorerWidth": 350,
                        "variableToType": {},
                        "headers": "{\n  \"x-cassandra-token\": \"populate_me\"\n}",
                        "isFile": false,
                        "name": "graphql-schema"
                    },
                    "ckhmat9oe00023h5oatstmmiv": {
                        "id": "ckhmat9oe00023h5oatstmmiv",
                        "query": "# Write your query or mutation here\n",
                        "variables": "",
                        "responses": [],
                        "endpoint": "/graphql/system",
                        "hasMutation": false,
                        "hasSubscription": false,
                        "hasQuery": false,
                        "queryTypes": {
                            "firstOperationName": null,
                            "subscription": false,
                            "query": false,
                            "mutation": false
                        },
                        "subscriptionActive": false,
                        "date": "2020-11-17T18:16:39.066Z",
                        "starred": false,
                        "queryRunning": false,
                        "operations": [],
                        "isReloadingSchema": false,
                        "isSchemaPendingUpdate": false,
                        "responseExtensions": {},
                        "queryVariablesActive": false,
                        "endpointUnreachable": false,
                        "editorFlex": 1,
                        "variableEditorOpen": false,
                        "variableEditorHeight": 200,
                        "responseTracingOpen": false,
                        "responseTracingHeight": 300,
                        "docExplorerWidth": 350,
                        "variableToType": {},
                        "headers": "{\n  \"x-cassandra-token\": \"populate_me\"\n}",
                        "isFile": false,
                        "name": "graphql"
                    }
                },
                "selectedSessionId": "ckhmat9oe00013h5o792k7kqp",
                "sessionCount": 1,
                "headers": ""
            },
            "sharing": {
                "history": false,
                "headers": true,
                "allTabs": true,
                "shareUrl": null
            },
            "history": {}
        }
    },
    "selectedWorkspace": "/graphql-schema",
    "settingsString": {
        "editor.cursorShape": "line",
        "editor.fontFamily": "'Source Code Pro', 'Consolas', 'Inconsolata', 'Droid Sans Mono', 'Monaco', monospace",
        "editor.fontSize": 14,
        "editor.reuseHeaders": true,
        "editor.theme": "dark",
        "general.betaUpdates": false,
        "prettier.printWidth": 80,
        "prettier.tabWidth": 2,
        "prettier.useTabs": false,
        "request.credentials": "omit",
        "schema.disableComments": true,
        "schema.polling.enable": true,
        "schema.polling.endpointFilter": "*localhost*",
        "schema.polling.interval": 2000,
        "tracing.hideTracingResponse": true,
        "tracing.tracingSupported": true
    },
    "stateInjected": false,
    "appHistory": {
        "items": {}
    },
    "general": {
        "historyOpen": false,
        "fixedEndpoint": false,
        "endpoint": "",
        "configString": "",
        "envVars": {}
    }
}
````

I see the session section corresponding to the two default tabs.  Lets modify those, add the auth token, and re-import this back to GraphQL Playground.  This will be a great basic test of export/import exposed by the original poster.   I tried quite a few attempts and although I am able to edit the object, import, and see the changed values, nothing in the UI changes.  If i reload, nothing indicates a change and I do not see the new object values.  If if i refresh the page I am stuck back with the default workbook.   

Next I inspect the sessions and notice the sessions are different.  If the session is not persisting this means the solution will not work. So i chat up a few internal resources and got linked back to another [GitHub Issue #797](https://github.com/graphql/graphql-playground/issues/797).  Yikes...  To make further progress on this post I am going to have to build my own graphQL UI.  



