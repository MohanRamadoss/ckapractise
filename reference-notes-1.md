# K8S

```
kubectl config set-context --current --namespace=
```

## Certification

### core

- pod
- deployment
- replicaSets
- namespaces

### configurations

#### ConfigMaps

- imperative
  - use `kubectkl create configMap <name> --from-literal=<key>=<value>`
  - use `kubectkl create configMap <name> --from-from=<path>`
- declarative
  - kind: ConfigMaps(v1)
    - no spec, data install, then key: value pairs
- use configmap in pods

  ```yaml
  spec:
    containers:
      - name: pod-name
        image: docker/pod
        ports:
          - containerPort: 8080
        envFrom:
          - configMapRef:
            name: app-config
            # key: APP_COLOR (single env)
  ```

#### Secret

- imperative: similar to configMap
- declarative

  ```yaml
  spec:
    envFrom:
      - secretRef:
        name: app-config
  ```

or inject from volume

```yaml
spec:
  volumes:
    - name: app-secret-volume
      secret:
        secretName: app-secret
```

#### SecurityContext

- declarative on container or pod level

```yaml
spec:
  securityContext:
    runAsUser: 1000
    capabilities:
      add: ['MAC_ADMIN']
```

#### Resource

```yaml
spec:
  containers:
    - name: image-name
      resources:
        request:
          memory: '1Gi'
          cpu: 1
        limits:
          memory: '2Gi'
          cpu: 2
```

### Taint & Tolerations

- `kubectl tain nodes node-1 app=blue:NoSchedule`
- pod.yaml

```yaml

---
spec:
  containers:
    - name: nginx
      image: nginx
  tolerations:
    - key: 'app'
      operator: 'Equal'
      value: 'blue'
      effect: 'NoSchedule'
```

## Node Affinity

```yaml
spec:
  containers: ...
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: app_type
                operator: In
                values:
                  - beta
```

### Service account

- `kubectl create sa dashboard-sa` it will then create a secret token e.g. dashboard-sa-token-kbbdn
  - `kubectl describe secret dashboard-sa-token-kbbdn`
- add to pod with spec.serviceAccount

## Topics

### Multi-container Pod

```yaml
spec:
  containers:
    - name: simple-webapp
      image: docker/simple-webapp
      ports:
        - containerPort: 8080
    - name: log-agent
      image: docker/log-agent
    - name: ...
```

- logs design patterns:
  - sidecar(small container along with the main app)
  - adapter(every main app send to one small container before save to DB)
  - ambassador(send to a centralized small container and then save to DB)

### Observability

- spec.readinessProbe: health check to make sure the app is really running after deploying

```yaml
spec:
  readinessProbe:
    httpGet:
      path: /ready
      port: 8080
    initialDelaySeconds: 10
    periodSeconds: 5
    failureThreshold: 8
```

- spec.livenessProbe
  - health check to avoid when the pod is running but service is down

### Labels

- labels
  - use metadata.labels
  - or use `kubectl label pods pod-name key=value`
  - or use `kubectl run my-server --image=nginx --labels="key=value,env=prod"`
  - select `kubectl get pods --selector env=pod`
- spec.selector
  - spec.selector.matchLabels.key: value
- example: rs uses selector to find the pod, then service uses the label to connect to the pod in the rs
  - ![selector](https://i.imgur.com/BhNmKLD.png)
- metadata.annotations

### Update & Deployment

- spec.strategy.type can be “Recreate” or “RollingUpdate”(the default value)
- rolling update
  - `kubectl apply -f <file>` will trigger a rolling updates
- rollout
  - use `kubectl rollout status deployment/myapp-deployment`
  - use `kubectl rollout history deployment/myapp-deployment`

### Job/CronJob

- kind: Job(batch/v1beta1), need a pod in spec.template
  - spec.schedule: "_/1 _ \* \* \*"
- kind: CronJob(batch/v1), need a pod in spec.template

### Services\*\* use selector to apply to p

- kind: Service(v1)
  - spec.type: NodePort
    - ![NodePort](https://i.imgur.com/rn8Vgg8.png)
    - spec.ports
      - targetPort
      - port
      - nodePort (to the outside world)
    - spec.selector
  - spec.type: ClusterIP
    - spec.ports
      - targetPort
      - port (act like a load balancer)
    - spec.selector

### Ingress

- Ingress Controller = Deployment + Service + ConfigMap + Auth
  - ![ingress-controller](https://i.imgur.com/CiPDmgU.png)
- kind: Ingress - Ingress resources

  - one service

  ```yaml
  apiVersion: networking.k8s.io/v1beta1
  kind: Ingress
  metadata:
    name: test-ingress
  spec:
    backend:
      serviceName: testsvc
      servicePort: 80
  ```

  - route to multiple services

  ```yaml
  apiVersion: networking.k8s.io/v1beta1
  kind: Ingress
  metadata:
    name: simple-fanout-example
    annotations:
      nginx.ingress.kubernetes.io/rewrite-target: /
      # (O) http://<ingress-service>:<ingress-port>/watch --> http://<watch-service>:<port>/
      # (X) http://<ingress-service>:<ingress-port>/watch --> http://<watch-service>:<port>/watch
  spec:
    rules:
      - host: foo.bar.com
        http:
          paths:
            - path: /foo
              backend:
                serviceName: service1
                servicePort: 4200
            - path: /bar
              backend:
                serviceName: service2
                servicePort: 8080
  ```

  - route to sub domain with multiple services

  ```yaml
  spec:
    rules:
      - host: foo.bar.com
        http:
          paths:
            - backend:
                serviceName: service1
                servicePort: 80
      - host: bar.foo.com
        http:
          paths:
            - backend:
                serviceName: service2
                servicePort: 80
  ```

### State Persistence

- spec.volumes
  - spec.volumes.hostPath.path
  - spec.containers.volumeMounts
- kind: PersistentVolume(v1)
  - spec.accessModes
  - capacity.storage
- kind: PersistentVolumeClaim(v1)
  - or use it in the pod, spec.volumes.persistentVolumeClaim.claimName

---
