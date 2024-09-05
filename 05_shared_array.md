# Shared Array

https://grafana.com/docs/k6/latest/javascript-api/k6-data/sharedarray

https://grafana.com/docs/k6/latest/examples/data-parameterization/#from-a-csv-file

```js
import http from 'k6/http'
import { check, fail } from 'k6'
import { Counter } from 'k6/metrics'
import { SharedArray } from 'k6/data'
import { URL } from 'https://jslib.k6.io/url/1.0.0/index.js'

const tps = new Counter('tps')

export const options = {
  scenarios: {
    constant_load: {
      executor: 'constant-vus',
      vus: 2,
      duration: '5s',
      gracefulStop: '1s',
    },
  },
}

const fruits = new SharedArray('fruits', function () {
  return JSON.parse(open('fruits.json'))
})

export default function () {
  const fruit = fruits[Math.floor(Math.random() * fruits.length)]
  console.log(fruit)

  const url = new URL('http://localhost:8080/fruits')
  const res = http.get(url.toString())
  res.body === null && fail('http.get failed')
  const body = res.json()

  const ok = check(res, {
    'status_code is 200': () => res.status === 200,
    'code is 200': () => body.code === 200,
    '26 fruits': () => body.data && body.data.length === 26,
  })

  ok && tps.add(1)

  ok && console.info(`${body.code} 👉 count: ${body.count}`)
  !ok && console.error(`${body.code} 👉 ${body.message}`)
}
```

## CSV

```js
const data = new SharedArray('data', function () {
  return papaparse.parse(open('./data.csv'), { header: true }).data;
});

export function getData(){
  const users = merchants[scenario.iterationInTest % data.length];
  // ...
}
```
