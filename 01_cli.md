# CLI

## Install

https://github.com/grafana/xk6

https://grafana.com/docs/k6/latest/extensions/explore

```sh
go install go.k6.io/xk6/cmd/xk6@v0.13
xk6 build v0.54.0 \
  --with github.com/grafana/xk6-output-influxdb@v0.5.0 \
  --with github.com/szkiba/xk6-dotenv@v0.2.0 \
  --with github.com/avitalique/xk6-file@v1.4.2 \
  --with github.com/grafana/xk6-faker@v0.4.0 \
  --output $(go env GOPATH)/bin
```

https://k6.io/docs/get-started/running-k6

## Create an example

```sh
k6 new
```

```sh
k6 run script.js
```

## k6 run

* `-u int` number of virtual users (default 1)
* `-d string` test duration limit
* `-i int` total iteration limit (among all VUs)
* `-s string` add a stage as `[duration]:[target]`
* `-p` start the test in a paused state
* `--no-setup` don't run `setup()`
* `--no-teardown` don't run `teardown()`
* `--rps int` limit requests per second
* `--insecure-skip-tls-verify` skip verification of TLS certificates
* `-w` throw warnings (like failed http requests) as errors
* `--include-system-env-vars` pass the real system environment variables to the runtime (default true)
* `--compatibility-mode string` JavaScript compiler compatibility mode
    * `base`: pure Sobek - Golang JS VM supporting ES6+
    * `extended` (default): base + sets "global" as alias for "globalThis"
    * `experimental_enhanced`: esbuild-based transpiling for TypeScript and ES6+ support
* `-e k=v` add/override environment variable with VAR=value
* `--no-thresholds` don't run thresholds
* `--no-summary` don't show the summary at the end of the test
* `--summary-export string` output the end-of-test summary report to JSON file
* `-o string` uri for an external metrics database

```sh
k6 run --vus 10 --duration 30s script.js
```
