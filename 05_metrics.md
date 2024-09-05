# Metrics

https://grafana.com/docs/k6/latest/using-k6/metrics

```js
import http from 'k6/http'
import { check, fail } from 'k6'
import { Counter, Rate, Gauge, Trend } from 'k6/metrics'

const tps = new Counter('tps')
const successRate = new Rate('success_rate')
const failureRate = new Rate('failure_rate')
const contentSize = new Gauge('content_size')
const responseTime = new Trend('response_time')

export const options = {
  vus: 2,
  duration: '5s',
  thresholds: {
    http_req_failed: [
      'rate<0.01', // error < 1%
      { threshold: 'rate<0.5', abortOnFail: true }, // abort test when failure_rate >= 50%
    ],
    http_req_duration: ['p(95)<600'], // 95% of response time < 600ms
    tps: ['rate>=9.5'], // >= 9.5 TPS
    success_rate: ['rate>=0.9'], // >= 90%
    failure_rate: [
      { threshold: 'rate<=0.1', abortOnFail: false }, // <= 10%
      { threshold: 'rate<0.5', abortOnFail: true }, // abort test when failure_rate >= 50%
    ],
    content_size: ['value>=27'],
    response_time: ['p(95)<200'],
  },
}

export default function () {
  const res = http.get('http://localhost:8080/fruits')
  res.body === null && fail('http.get failed')
  const body = res.json()

  const ok = check(res, {
    'status_code is 200': () => res.status === 200,
    'code is 200': () => body.code === 200,
  })

  ok && tps.add(1)
  ok && contentSize.add(body.data.length)
  successRate.add(ok)
  failureRate.add(!ok)
  responseTime.add(res.timings.duration)

  ok && console.info(`${body.code} 👉 count: ${body.count}`)
  !ok && console.error(`${body.code} 👉 ${body.message}`)
}
```
