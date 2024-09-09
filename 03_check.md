# Check

https://grafana.com/docs/k6/latest/using-k6/checks

```js
import http from 'k6/http'
import { check } from 'k6'
import { URL } from 'https://jslib.k6.io/url/1.0.0/index.js'

export const options = {
  vus: 2,
  duration: '5s',
}

export default function() {
  const url = new URL('https://jsonplaceholder.typicode.com/todos/1')
  http.get(url)

  check(res, { 
      'status_code is 200': (r) => r.status === 200,
      'body_size is less than 100 bytes': (r) => r.body.length < 100,
  })
}
```
