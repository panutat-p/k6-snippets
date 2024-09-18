# Summary

## Print

```js
export function handleSummary(data) {
  const result = {
    iterations: data.metrics.iterations,
    tps: data.metrics.tps,
  }
  return {
    stdout: JSON.stringify(result, null, 2),
  }
}
```

## Export to file

```js
export function handleSummary(data) {
  return {
    'summary.json': JSON.stringify(data, null, 2),
  }
}
```

## textSummary

```js
import http from 'k6/http'
import { Counter } from 'k6/metrics'
import { URL } from 'https://jslib.k6.io/url/1.0.0/index.js'
import { textSummary } from 'https://jslib.k6.io/k6-summary/0.1.0/index.js'

const tps = new Counter('tps')

export const options = {
  vus: 2,
  duration: '5s',
}

export default function () {
  const url = new URL('http://localhost:8080/fruits')
  http.get(url.toString())
  tps.add(1)
}

export function handleSummary(data) {
  delete data.metrics["http_reqs"];
  delete data.metrics["http_req_failed"];
  delete data.metrics["http_req_duration{expected_response:true}"];
  delete data.metrics["http_req_tls_handshaking"];
  delete data.metrics["http_req_blocked"];
  delete data.metrics["http_req_connecting"];
  delete data.metrics["http_req_receiving"];
  delete data.metrics["http_req_sending"];
  delete data.metrics["http_req_waiting"];

  return {
    stdout: textSummary(data, { indent: "→", enableColors: true }),
  }
}
```
