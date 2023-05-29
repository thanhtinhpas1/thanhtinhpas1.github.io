---
layout: post
title:  "Query string in React"
summary: "Query string in React"
author: thanhtinhpas1
date: '2023-05-28 14:35:23 +0530'
category: react
thumbnail: https://geekflare.com/wp-content/uploads/2022/08/Query-String-Value-poster2-1.jpg
keywords: Query string in React
permalink: /blog/query-string-in-react
usemathjax: true
---

# Query string in React

## Usage
Link npm: [query-string](https://www.npmjs.com/package/query-string)

Install
```bash
npm install query-string
```

In react, using as example:

```javascript
import {useHistory} from 'react-router-dom';
import queryString from 'query-string';

const history = useHistory();
const search = history.local.search;

const parsed = queryString.parse(search);
console.log(parsed);
// => {foo: 'bar'}

const parsedHash = queryString.parse(history.local.hash);
console.log(parsedHash);
// => {token: 'bada55cafe'}

parsed.foo = 'unicorn';
parsed.like = 'pizza';

const stringified = queryString.stringify(parsed);
// => 'foo=uincorn&ilike-pizza'

location.search = stringified;

// note that `location.search` automatically prepends a question mark
console.log(history.local.search);
// => '?foo=unicorn&ilike=pizza'
```

## Append query string
### Installation
```bash
npm install append-query
```

`appendQuery(url, query[, options])`

- **url** - a string url to append to
- **query** - a string or object containing query params to append
- **options** (optional)
    *encodeComponent* - whether or not to en coded appended passed params
    *removeNull* - whether or not to remove params for null properties in the query object.

```javascript
var appendQuery = require('append-query')

appendQuery('http://example.com/foo', 'bar=baz&beep=boop')
// http://example.com/foo?bar=baz&beep=boop

appendQuery('http://example.com/?foo=bar', 'hello=world')
// http://example.com/?foo=bar&hello=world

appendQuery('http://example.com/', { beep: 'boop' })
// http://example.com/?beep=boop

appendQuery('http://example.com/', { beep: 'boop' })
// http://example.com/?beep=boop

// using pre-encoded values
appendQuery('http://example.com/', { preEncoded: '%22hello%2C%20world!%22' }, { encodeComponents: false })
// http://example.com/?preEncoded=%22hello%2C%20world!%22

// remove existing values
appendQuery('http://example.com/?test=1', { test: null }, { removeNull: true })
// http://example.com/
```