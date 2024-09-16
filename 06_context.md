# Context

https://grafana.com/docs/k6/latest/javascript-api/k6-execution

```js
import exec from 'k6/execution'
import { scenario, vu } from 'k6/execution'
import { sleep } from 'k6'

export const options = {
  scenarios: {
    s1: {
      executor: 'shared-iterations',
      vus: 10,
      iterations: 100,
      maxDuration: '10s',
    },
  },
}

export function setup() {
  console.log(`start_time: ${new Date(scenario.startTime)}`)
}

export default function () {
  console.info(`elapsed_time: ${exec.instance.currentTestRunDuration}`)
  console.info(`vu_id: ${vu.idInTest}`)
  console.info(`i: ${scenario.iterationInTest}`)
  sleep(2)
}
```
