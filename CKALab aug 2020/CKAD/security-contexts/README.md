# Practice Test - Security Contexts

## Exercises

**What is the user used to execute the sleep process within the 'ubuntu-sleeper' pod?**

```bash
$ kubectl exec ubuntu-sleeper whoami
```

**Edit the pod 'ubuntu-sleeper' to run the sleep process with user ID 1010.**

> Note: Only make the necessary changes. Do not modify the name or image of the pod.

```yaml
spec:
  securityContext:
      runAsUser: 1010
```


**Update pod 'ubuntu-sleeper' to run as Root user and with the 'SYS_TIME' capability.**

```yaml
containers:
  - securityContext:
      capabilities:
        add: ["SYS_TIME"]
```
