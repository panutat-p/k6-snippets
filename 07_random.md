# utils

https://grafana.com/docs/k6/latest/javascript-api/jslib/utils

## Random

```js
import { randomString, randomIntBetween } from 'https://jslib.k6.io/k6-utils/1.5.0/index.js'

export function randomFullName() {
  return `${randomString(10)} ${randomString(10)}`
}

export function randomMobileNo() {
  const prefix = '08'
  const middle = randomIntBetween(0, 9)
  const part1 = randomIntBetween(100, 999)
  const part2 = randomIntBetween(1000, 9999)
  return `${prefix}${middle}-${part1}-${part2}`
}
```
