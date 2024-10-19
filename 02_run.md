# Introduction

https://grafana.com/docs/k6/latest/using-k6/http-requests

## Duration

```js
import http from 'k6/http'
import { URL } from 'https://jslib.k6.io/url/1.0.0/index.js'

export const options = {
  vus: 2,
  duration: '5s',
  maxDuration: '1h',
}

export function setup() {
  console.info('hello')
}

export default function() {
  const url = new URL('https://jsonplaceholder.typicode.com/todos/1')
  http.get(url.toString())
}
```

## Iterations

```js
import http from 'k6/http'
import { URL } from 'https://jslib.k6.io/url/1.0.0/index.js'

export const options = {
  vus: 2,
  iterations: 10,
  maxDuration: '1h',
}

export function setup() {
  console.info('hello')
}

export default function() {
  const url = new URL('https://jsonplaceholder.typicode.com/todos/1')
  http.get(url.toString())
}
```

## Life cycle

```js
import { sleep } from 'k6'
import { vu } from 'k6/execution'

console.warn('init') // This will be printed 6 times

export const options = {
  vus: 3,
  iterations: 3,
  maxDuration: '1h',
}

export function setup() {
  console.warn('setup') // This will be printed once
  const nums = [1, 2, 3]
  return { nums }
}

export default function(data) {
  console.info(`n: ${data.nums[vu.idInTest - 1]}`)
  sleep(1)
}

export function teardown(data) {
  console.warn('teardown') // This will be printed once
}
```
