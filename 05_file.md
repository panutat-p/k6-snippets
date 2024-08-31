# File

## SharedArray

```js
import { SharedArray } from 'k6/data';

import papaparse from 'https://jslib.k6.io/papaparse/5.1.1/index.js';

const config = {
  scenarios: {
    getUser:{
      vus: 10,
    },
  },
  thresholds: {},
  discardResponseBodies: false,
};

const t = 1;
config.scenarios.getUser.vus = Math.round(config.scenarios.getUser.vus * t);

const merchants = new SharedArray('profileMerchants', function () {
  return papaparse.parse(open('./profiles.csv'), { header: true }).data;
});

export function getProfileInquiry(){
  const users = merchants[scenario.iterationInTest % merchants.length];
  // ...
}
```

## papaparse

https://gist.github.com/deniscapeto/d05544347dd903636e71ee74ea0d9950

```js
import papaparse from 'https://jslib.k6.io/papaparse/5.1.1/index.js';
import { SharedArray } from "k6/data";

const csvData = new SharedArray("another data name", function() {
    return papaparse.parse(open('./data.csv'), { header: true }).data;
});
```
