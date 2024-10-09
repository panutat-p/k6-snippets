# File

https://github.com/avitalique/xk6-file

## Write

```js
import file from 'k6/x/file'

export default function () {
  const fruits = [
    {"name": "apple", "color": "red", "price": 50.50},
    {"name": "banana", "color": "yellow", "price": 8.00},
    {"name": "cherry", "color": "red", "price": 6.50},
  ]
  const data = JSON.stringify(fruits)
  file.writeString('fruit.json', data)
}
```
