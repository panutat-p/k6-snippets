# Docker Image

https://hub.docker.com/_/golang

https://hub.docker.com/_/alpine

https://github.com/grafana/xk6-output-influxdb/blob/main/Dockerfile

```sh
docker buildx build \
--platform linux/amd64,linux/arm64  \
-t admin/k6:latest \
-f Dockerfile \
.
```

```sh
docker container run \
  --rm \
  -it \
  --name k6 \
  --mount type=bind,source="$(pwd)",target=/home/k6 \
  admin/k6 \
  /bin/sh
```

## Bookworm

```dockerfile
FROM golang:bookworm
WORKDIR /go
RUN apt update && \
    apt install -y ca-certificates && \
    rm -rf /var/lib/apt/lists/* && \
    groupadd -g 1001 k6 && useradd -m -u 1001 -g 1001 k6
RUN go install go.k6.io/xk6/cmd/xk6@v0.13 && \
    xk6 build v0.54.0 \
      --with github.com/avitalique/xk6-file@v1.4.2 \
      --with github.com/szkiba/xk6-dotenv@v0.2.0 \
      --with github.com/grafana/xk6-faker@v0.4.0 \
      --with github.com/grafana/xk6-sql@v0.4.1 \
      --with github.com/grafana/xk6-amqp@v0.4.1 \
      --with github.com/grafana/xk6-output-influxdb@v0.5.0 \
      --output /go/bin/k6
USER 1001
WORKDIR /home/k6
```

## Bookworm slim

```dockerfile
FROM golang:bookworm AS builder
WORKDIR /go
RUN GOBIN=/tmp go install github.com/go-task/task/v3/cmd/task@v3.39
RUN go install go.k6.io/xk6/cmd/xk6@v0.13 && \
    xk6 build v0.54.0 \
      --with github.com/avitalique/xk6-file@v1.4.2 \
      --with github.com/szkiba/xk6-dotenv@v0.2.0 \
      --with github.com/grafana/xk6-faker@v0.4.0 \
      --with github.com/grafana/xk6-sql@v0.4.1 \
      --with github.com/grafana/xk6-amqp@v0.4.1 \
      --with github.com/grafana/xk6-output-influxdb@v0.5.0 \
      --output /tmp/k6


FROM debian:bookworm-slim AS runtime
RUN apt update && \
    apt install -y ca-certificates && \
    rm -rf /var/lib/apt/lists/* && \
    groupadd -g 1001 k6 && \
    useradd -m -u 1001 -g 1001 k6
COPY --from=builder /tmp/task /usr/local/bin/task
COPY --from=builder /tmp/k6 /usr/local/bin/k6
USER 1001
WORKDIR /home/k6
```

## Alpine

```dockerfile
FROM golang:1.23-alpine AS builder
WORKDIR /go
RUN GOBIN=/tmp go install github.com/go-task/task/v3/cmd/task@v3.39
RUN go install go.k6.io/xk6/cmd/xk6@v0.13 && \
    xk6 build v0.54.0 \
      --with github.com/avitalique/xk6-file@v1.4.2 \
      --with github.com/szkiba/xk6-dotenv@v0.2.0 \
      --with github.com/grafana/xk6-faker@v0.4.0 \
      --with github.com/grafana/xk6-sql@v0.4.1 \
      --with github.com/grafana/xk6-amqp@v0.4.1 \
      --with github.com/grafana/xk6-output-influxdb@v0.5.0 \
      --output /tmp/k6


FROM alpine:3 AS runtime
RUN apk add --no-cache ca-certificates && \
    adduser -D -u 1001 -g 1001 k6
COPY --from=builder /tmp/task /usr/local/bin/task
COPY --from=builder /tmp/k6 /usr/local/bin/k6
USER 1001
WORKDIR /home/k6
```

## Compose

```yaml
services:
  xk6:
    image: admin/k6:latest
    command: 
      - run
      - script.js
    volumes:
      - type: bind
        source: ${HOME}/k6-script
        target: /home/k6
```

## Pod

```sh
kubectl create configmap k6-script-configmap --from-file=path/to/script
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: k6-custom-pod
spec:
  containers:
    - name: k6
      image: admin/k6:latest
      args: 
        - run
        - script.js
      volumeMounts:
        - name: k6-script
          mountPath: /home/k6
  volumes:
    - name: k6-script
      configMap:
        name: k6-script-configmap
  restartPolicy: Never
```
