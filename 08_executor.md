# Executor

https://grafana.com/docs/k6/latest/using-k6/scenarios/executors

## Constant VUs

```js
import http from 'k6/http';
import { check } from 'k6';
import { URL } from 'https://jslib.k6.io/url/1.0.0/index.js';

export const options = {
  scenarios: {
    s1: {
      executor: 'constant-vus',
      vus: 2,
      duration: '5s',
    },
  },
};

export default function() {
  const url = new URL('https://jsonplaceholder.typicode.com/todos/1');
  const res = http.get(url.toString());

  check(res, { 
    'status_code is 200': (r) => r.status === 200,
    'body_size is less than 100 bytes': (r) => r.body.length < 100,
  });
}
```

## Ramping VUs

```js
import http from 'k6/http';
import { check } from 'k6';
import { URL } from 'https://jslib.k6.io/url/1.0.0/index.js';

export const options = {
  scenarios: {
    s2: {
      executor: 'ramping-vus',
      stages: [
        { duration: '3s', target: 10 },
        { duration: '5s', target: 20 },
        { duration: '3s', target: 0 },
      ],
    },
  },
};

export default function() {
  const url = new URL('https://jsonplaceholder.typicode.com/todos/1');
  const res = http.get(url.toString());

  check(res, { 
    'status_code is 200': (r) => r.status === 200,
    'body_size is less than 100 bytes': (r) => r.body.length < 100,
  });
}
```

## Shared iterations

```js
import http from 'k6/http'
import { check } from 'k6'
import { URL } from 'https://jslib.k6.io/url/1.0.0/index.js'

export const options = {
  scenarios: {
    s3: {
      executor: 'shared-iterations',
      vus: 2,
      iterations: 50,
      maxDuration: '5s',
    },
  },
}

export default function() {
  const url = new URL('https://jsonplaceholder.typicode.com/todos/1');
  const res = http.get(url.toString());

  check(res, { 
    'status_code is 200': (r) => r.status === 200,
    'body_size is less than 100 bytes': (r) => r.body.length < 100,
  });
}
```

## Per VU iterations

```js
import http from 'k6/http'
import { check } from 'k6'
import { URL } from 'https://jslib.k6.io/url/1.0.0/index.js'

export const options = {
  scenarios: {
    s4: {
      executor: 'per-vu-iterations',
      vus: 2,
      iterations: 10,
      maxDuration: '10s',
    },
  },
}

export default function() {
  const url = new URL('https://jsonplaceholder.typicode.com/todos/1')
  const res = http.get(url.toString())

  check(res, { 
    'status_code is 200': (r) => r.status === 200,
    'body_size is less than 100 bytes': (r) => r.body.length < 100,
  })
}
```
