# Practice Test - Resource Requirements and Limits

"When a pod is created the containers are assigned a default CPU request of .5 and memory of 256Mi". For the POD to pick up those defaults you must have first set those as default values for request and limit by creating a LimitRange in that namespace.

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:
      memory: 512Mi
    defaultRequest:
      memory: 256Mi
    type: Container
```

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-limit-range
spec:
  limits:
  - default:
      cpu: 1
    defaultRequest:
      cpu: 0.5
    type: Container
```

## Exercises

**A pod named 'rabbit' is deployed. Identify the CPU requirements set on the Pod in the current(default) namespace**

```bash
$ kubectl describe pod rabbit
```

**The elephant runs a process that consume 15Mi of memory. Increase the limit of the elephant pod to 20Mi.**

```bash
$ kubectl get pod elephant -o yaml > elephant-pod.yaml
# Edit memory limit
$ kubectl apply -f elephant-pod.yaml
```