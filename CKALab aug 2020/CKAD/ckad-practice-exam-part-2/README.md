# CKAD Practice Exam: Part 2

## Additional Information and Resources

Our team has a pod that generates some log output. However, they want to consume the data using an external application, which requires the data to be in a specific format. Our task is to create a pod design that utilizes an adapter running `fluentd` to format the output from the main container.

* There is a `fluentd` configuration located on the server at `/usr/ckad/fluent.conf`. Load the data from this file into a ConfigMap called `fluentd-config`.
* Create the pod descriptor in `/usr/ckad/adapter-pod.yml`. An empty file has already been created for us.
* The pod should be named `counter`.
* Add a container to the pod that runs the `busybox` image, and name it `count`.
* Run the `count` container with the following arguments:

```bash
- /bin/sh
- -c
- >
  i=0;
  while true;
  do
    echo "$i: $(date)" >> /var/log/1.log;
    echo "$(date) INFO $i" >> /var/log/2.log;
    i=$((i+1));
    sleep 1;
  done
```

* Add another container called `adapter` to the pod, and make it run the `k8s.gcr.io/fluentd-gcp:1.30` image.
* Add an environment variable to the `adapter` container called `FLUENTD_ARGS` with the value `-c /fluentd/etc/fluent.conf`.
* Mount the `fluentd-config` ConfigMap to the `adapter` container so that the config data is located inside the container in a file at `/fluentd/etc/fluent.conf`.
* Create a volume for the pod in such a way that the storage will be deleted if the pod is removed from a node. Mount this volume to both containers at `/var/log`. The `count` container will output log data to this volume, and the adapter container will read the data from the same volume.
* Create a `hostPath` volume where the `adapter` container will output the formatted log data. Store the data at the `/usr/ckad/log_output` path. Mount the volume to the `adapter` container at `/var/logout`.

```bash
$ kubectl create configmap fluentd-config --from-file=/usr/ckad/fluent.conf
$ kubectl run counter --image=busybox --dry-run -o yaml > /usr/ckad/adapter-pod.yml
```

adapter-pod.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: counter
spec:
  volumes:
  - name: varlog
    emptyDir: {}
  - name: config-volume
    configMap:
      name: fluentd-config
  - name: logout
    hostPath:
      path: /usr/ckad/log_output
  containers:
  - name: count
    image: busybox
    args:
    - /bin/sh
    - -c
    - >
      i=0;
      while true;
      do
        echo "$i: $(date)" >> /var/log/1.log;
        echo "$(date) INFO $i" >> /var/log/2.log;
        i=$((i+1));
        sleep 1;
      done
    volumeMounts:
    - name: varlog
      mountPath: /var/log
  - name: adapter
    image: k8s.gcr.io/fluentd-gcp:1.30
    env:
    - name: FLUENTD_ARGS
      value: -c /fluentd/etc/fluent.conf
    volumeMounts:
    - name: varlog
      mountPath: /var/log
    - name: config-volume
      mountPath: /fluentd/etc
    - name: logout
      mountPath: /var/logout
```
