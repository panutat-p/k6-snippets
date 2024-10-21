# SQL

https://github.com/grafana/xk6-sql

https://github.com/grafana/xk6-sql-driver-mysql

## MySQL

```js
import sql from "k6/x/sql"
import driver from "k6/x/sql/driver/mysql"

export function setup() {
  const dsn = 'admin:1234@tcp(127.0.0.1:3306)/demo'
  const db = sql.open(driver, dsn)

  return { db }
}

export default function (data) {
  const db = data.db
  db.exec(`SELECT LAST_INSERT_ID()`)
}

export function teardown(data) {
  const db = data.db
  db.close()
}
```
