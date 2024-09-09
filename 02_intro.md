# Introduction

https://grafana.com/docs/k6/latest/using-k6/http-requests

## Duration

```js
import http from 'k6/http'
import { URL } from 'https://jslib.k6.io/url/1.0.0/index.js'

export const options = {
  vus: 2,
  duration: '5s',
}

export default function() {
  const url = new URL('https://jsonplaceholder.typicode.com/todos/1')
  http.get(url)
}
```

## Iterations

```js
import http from 'k6/http'
import { URL } from 'https://jslib.k6.io/url/1.0.0/index.js'

export const options = {
  vus: 2,
  iterations: 10,
}

export default function() {
  const url = new URL('https://jsonplaceholder.typicode.com/todos/1')
  http.get(url)
}
```

## stages

```js
import http from 'k6/http'
import { URL } from 'https://jslib.k6.io/url/1.0.0/index.js'
import { check } from 'k6'

export const options = {
  stages: [
    { duration: '10s', target: 20 },
    { duration: '5s', target: 10 },
    { duration: '10s', target: 0 },
  ],
}

export default function() {
  const url = new URL('https://jsonplaceholder.typicode.com/todos/1')
  http.get(url)
}
```
