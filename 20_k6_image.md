# xk6 pod

https://hub.docker.com/_/golang

https://hub.docker.com/r/grafana/k6

https://hub.docker.com/r/grafana/xk6

## Dockerfile

https://github.com/grafana/xk6-output-influxdb/blob/main/Dockerfile

`Dockerfile`
```dockerfile
FROM golang:1.23-alpine as builder
WORKDIR $GOPATH/src/go.k6.io/k6
ADD . .
RUN apk --no-cache add git
RUN go install go.k6.io/xk6/cmd/xk6@v0.13
RUN xk6 build v0.54.0 \
  --with github.com/grafana/xk6-output-influxdb@v0.5.0 \
  --with github.com/szkiba/xk6-dotenv@v0.2.0 \
  --with github.com/avitalique/xk6-file@v1.4.2 \
  --with github.com/grafana/xk6-faker@v0.4.0 \
  --output /tmp/k6

FROM alpine:3.20
RUN apk add --no-cache ca-certificates && \
    adduser -D -u 10001 -g 10001 k6
COPY --from=builder /tmp/k6 /usr/bin/k6

USER 10001
WORKDIR /home/k6
ENTRYPOINT ["k6"]
```

```sh
docker buildx build \
--platform linux/amd64,linux/arm64  \
-t k6-custom:latest \
-f Dockerfile \
.
```

## Compose

```yaml
services:
  xk6:
    image: k6-custom:latest
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
    - name: k6-custom
      image: k6-custom:latest
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
