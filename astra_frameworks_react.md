---
layout: default
title: Astra + React
description: Connect your existing react app to Astra
permalink: /astra/application/frameworks/react/
---

# How I Connected A React App To Astra

I got involved in a React App Demo which had no backend database solution.  My quick answer was to use Astra.   In order to connect the existing react app to Astra I needed to improve my react project with astra authorization and astra api calls.   

I started by evaluating our sample React Apps:

1.	[React.js, JAMStack, REST Todo List](https://github.com/DataStax-Examples/todo-astra-jamstack-netlify)
2.	[Node.js, React.js, Serverless Todo List](https://github.com/tjake/todo-astra-react-serverless)

I decided on todo-astra-jamstack-netlify because it included functions for getOrcreateAuthToken and sample create, delete, and get functions that were very easy to understand.

First I needed to create my version of getOrcreateAuthToken.js:

```js
const fetch = require('cross-fetch')
const ENDPOINT = `https://${process.env.REACT_APP_ASTRA_DB_ID}-${process.env.REACT_APP_ASTRA_DB_REGION}.apps.astra.datastax.com/api/rest/v1`;
const REACT_APP_ASTRA_DB_USERNAME = process.env.REACT_APP_ASTRA_DB_USERNAME;
const REACT_APP_ASTRA_DB_PASSWORD = process.env.REACT_APP_ASTRA_DB_PASSWORD;

/*
 * This global variable will persist across requests throughout the life of the lambda.
 * We only need to fetch a token for the first request
 * Lambda creates new containers when the original one expires or if there is high concurrency
 * in which case the new containers will have to fetch their own tokens.
 */
let cachedToken;

export default async function getOrCreateAuthToken() {
  if (cachedToken) {
    return cachedToken;
  }
  try {
    //console.log(ENDPOINT)
    const response = await fetch(`${ENDPOINT}/auth`, {
      method: 'POST',
      headers: {
        'content-type': 'application/json',
      },
      body: JSON.stringify({
        username: REACT_APP_ASTRA_DB_USERNAME,
        password: REACT_APP_ASTRA_DB_PASSWORD,
      }),
    });
    cachedToken = await response.json();
    return cachedToken;
  } catch (e) {
    throw new Error('Could not authenticate with the Astra API');
  }
}
```

Next I needed to create an api.js in my react project:

```js
import utils from './utils';
import getOrCreateAuthToken from './getOrCreateAuthToken';

export const getMemes = async (sessionId) => {

  const fetch = require('cross-fetch');
  const ENDPOINT = `https://${process.env.REACT_APP_ASTRA_DB_ID}-${process.env.REACT_APP_ASTRA_DB_REGION}.apps.astra.datastax.com/api/rest/v1`;
  const REACT_APP_ASTRA_DB_KEYSPACE = process.env.REACT_APP_ASTRA_DB_KEYSPACE;
  const TABLE_NAME = 'memegen';
  const {authToken} = await getOrCreateAuthToken();

  try {
    const response = await fetch(`${ENDPOINT}/keyspaces/${REACT_APP_ASTRA_DB_KEYSPACE}/tables/${TABLE_NAME}/rows`, {
      headers: {
        'x-cassandra-token': authToken
      }
    });
    return response.json();
  } catch (e) {
    return JSON.stringify(e);
  }
};
```

Last I had to add calls to the api within my App.js:

```js
import {getMemes} from './utils/api';
...
...
componentDidMount() {
    const sessionId = this.getSessionId();
    getMemes(sessionId)
      .then(data => {
            this.setState({ images: data.rows.filter((i) => i.box_count === 2) });
        });
    //fetch("https://api.imgflip.com/get_memes")
    //  .then((r) => r.json())
    //  .then((images) => {
    //    console.log(images);
    //    this.setState({ images2: images.data.memes.filter((i) => i.box_count === 2) });
    //
```
:warning: Notice the commented code.  This was the original source of the memes.  The source is a JSON Object which i was easily able to import into Astra using a NiFi flow.  After this upgrade to the react app all memes are loaded from Astra.  

# What's Next?

Stay tuned for more updates to this react app, including saving memes to astra, improvments for Astra's new serverless auth mechanism, and a new api counter capability created just for this app.

{% include astra_help.html %}