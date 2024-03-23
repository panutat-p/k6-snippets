# Plugins

## xk6-kubernetes

https://github.com/grafana/xk6-kubernetes

```js
import { Kubernetes } from 'k6/x/kubernetes';
import { describe, expect } from 'https://jslib.k6.io/k6chaijs/4.3.4.3/index.js';

let json = {
    apiVersion: "v1",
    kind: "Pod",
    metadata: {
        name:      "poc-json-pod",
        namespace: "loadtest"
    },
    spec: {
        containers: [
            {
                name: "busybox",
                image: "busybox",
                command: ["sh", "-c", "sleep 10"]
            }
        ]
    }
}

export default function () {
    const kubernetes = new Kubernetes();

    describe('JSON-based resources', () => {
        const name = json.metadata.name
        const ns = json.metadata.namespace
        const helpers = kubernetes.helpers(ns)

        let pod

        describe('Create our pod using the JSON definition and wait until running', () => {
            pod = kubernetes.create(json)
            expect(pod.metadata, 'new pod').to.have.property('uid')
            expect(pod.status.phase, 'new pod status').to.equal('Pending')

            helpers.waitPodRunning(name, 10)

            let fetched = kubernetes.get("Pod", name, ns)
            expect(fetched.status.phase, 'status after waiting').to.equal('Running')
        })

        describe('Retrieve our pod by name and namespace, then execute a command within the pod', () => {
            let fetched = kubernetes.get("Pod", name, ns)
            expect(pod.metadata.uid, 'created and fetched uids').to.equal(fetched.metadata.uid)

            let greeting = 'hello xk6-kubernetes'
            let exec = {
                pod: name,
                container: fetched.spec.containers[0].name,
                command: ["echo", greeting]
            }
            let result = helpers.executeInPod(exec)
            const stdout = String.fromCharCode(...result.stdout)
            const stderr = String.fromCharCode(...result.stderr)
            expect(stdout, 'execution result').to.contain(greeting)
            expect(stderr, 'execution error').to.be.empty
        })
    })
}
```
