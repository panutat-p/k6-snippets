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
import { fail } from 'k6';
import { Rate, Counter } from "k6/metrics";
import http from "k6/http";
import { textSummary } from "https://jslib.k6.io/k6-summary/0.0.3/index.js";

const successRate = new Rate("success_rate");
const tpsTotal = new Counter("tps_total");
const tpsSuccess = new Counter("tps_success");
const BASE_URL="http://localhost:80"

export const options = {
  vus: 15,
  duration: '130s',
};

export function setup() {
  const healthCheckEndpoint = `${BASE_URL}`;
  const res = http.get(healthCheckEndpoint);
  console.log(res);
  if (res.status === 200 && res.body && res.json().code === 1000) {
    console.log("Health check succeeded")
  } else {
    fail("status code was *not* 200");
  }
}

export default function () {
  const res = http.get(`${BASE_URL}/api/v1/events`);

  tpsTotal.add(1);
  if (res.status === 200 && res.body && res.json().code === 1000) {
    successRate.add(1);
    tpsSuccess.add(1);
  } else {
    successRate.add(0);
  }
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
  };
}
```
