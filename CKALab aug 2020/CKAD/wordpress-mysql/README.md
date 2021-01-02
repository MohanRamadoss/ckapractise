# Challenge 1 - Wordpress MySQL

## Deploy the architecture 

### We could put all object definitions in one file though 

1. wordpress pv

* Volume Name: wordpress-persistent-storage
* Storage: 1Gi
* Access modes: ReadWriteMany
* NFS Path: /html

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: wordpress-persistent-storage
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  nfs:
    path: /html
    server: nfs01
```

`$ kubectl create -f wordpress-persistent-storage.yaml`

2. mysql pv

* Volume Name: mysql-persistent-storage
* Storage: 1Gi
* Access modes: ReadWriteMany
* NFS Path: /mysql

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-persistent-storage
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  nfs:
    path: /mysql
    server: nfs01
```

`$ kubectl create -f mysql-persistent-storage.yaml`

3. wordpress pvc

* Claim Name: wordpress-persistent-storage
* Storage Request: 1Gi
* Access modes: ReadWriteMany

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: wordpress-persistent-storage
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 1Gi
```

`$ kubectl create -f wordpress-pvc.yaml`

4. mysql pvc

* Claim Name: mysql-persistent-storage
* Storage Request: 1Gi
* Access modes: ReadWriteMany

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-persistent-storage
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 1Gi
```

`$ kubectl create -f mysql-pvc.yaml`

5. mysql-pass secret

* Secret Name: mysql-pass
* Secret: password=admin

`$ kubectl create secret generic mysql-pass --from-literal=password=admin`

6. wordpress deployment

* Name: wordpress
* Replicas: 2
* Image: wordpress
* Volume Mount: wordpress-persistent-storage
* Volume Mount Path: /var/www/html
* ENV: WORDPRESS_DB_PASSWORD=secret:mysql-pass

`$ kubectl create deployment wordpress --image=wordpress --dry-run -o yaml`

Edit YAML accordingly

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: wordpress
  name: wordpress
spec:
  replicas: 2
  selector:
    matchLabels:
      app: wordpress
  template:
    metadata:
      labels:
        app: wordpress
    spec:
      containers:
      - image: wordpress
        name: wordpress
        env:
          - name: WORDPRESS_DB_HOST
            value: wordpress-mysql
          - name: WORDPRESS_DB_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mysql-pass
                key: password
        volumeMounts:
        - mountPath: "/var/www/html"
          name: wordpress-persistent-storage
      volumes:
        - name: wordpress-persistent-storage
          persistentVolumeClaim:
            claimName: wordpress-persistent-storage
```

`$ kubectl create -f wordpress.yaml`

7. mysql deployment

* Name: mysql
* Replicas: 1
* Image: mysql:5.7
* ENV: MYSQL_ROOT_PASSWORD=secret:mysql-pass
* Volume Mount: mysql-persistent-storage
* Volume Mount Path: /var/lib/mysql

`$ kubectl create deployment mysql --image=mysql:5.7 --dry-run -o yaml`

Edit YAML accordingly

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: mysql
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - image: mysql:5.7
        name: mysql
        env:
          - name: MYSQL_ROOT_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mysql-pass
                key: password
        volumeMounts:
        - mountPath: "/var/lib/mysql"
          name: mysql-persistent-storage
      volumes:
        - name: mysql-persistent-storage
          persistentVolumeClaim:
            claimName: mysql-persistent-storage
```

`$ kubectl create -f mysql.yaml`

8. wordpress-mysql service

`$ kubectl expose deployment mysql --name=wordpress-mysql --type=ClusterIP --port=3306`

9. wordpress service

`$ kubectl expose deployment wordpress --name=wordpress --type=NodePort --port=80 --dry-run -o yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: wordpress
  name: wordpress
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 31004
  selector:
    app: wordpress
  type: NodePort
```

`$ kubectl create -f wordpress-svc.yaml`
