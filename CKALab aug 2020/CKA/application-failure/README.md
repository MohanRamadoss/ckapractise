# Practice Test - Application Failure

## Exercises

### Troubleshooting Test 1

**A simple 2 tier application is deployed in the alpha namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.**

`$ kubectl get all -n alpha`

#### Check Service Status

```bash
$ kubectl describe svc web-service -n alpha
$ curl http://web-service-ip:port
$ curl http://node-ip:nodePort
$ curl http://10.102.172.73:8080
$ curl http://172.17.0.39:30081
$ kubectl describe deployment webapp-mysql -n alpha
```

```yaml
Image:      mmumshad/simple-webapp-mysql
Port:       8080/TCP
Host Port:  0/TCP
Environment:
  DB_Host:      mysql-service
  DB_User:      root
  DB_Password:  paswrd
```

```bash
$ kubectl get svc -n alpha
$ kubectl edit svc -n alpha mysql # change the name from mysql -> mysql-service
$ kubectl delete svc -n alpha mysql
$ kubectl create -f /tmp/kubectl-edit-q9xos.yaml
```

### Troubleshooting Test 2

**The same 2 tier application is deployed in the beta namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue**

`$ kubectl get all -n beta`

#### Check Service Status

`$ kubectl describe svc mysql-service -n beta`

```yaml
Name:              mysql-service
Namespace:         beta
Labels:            <none>
Annotations:       <none>
Selector:          name=mysql
Type:              ClusterIP
IP:                10.98.98.78
Port:              <unset>  3306/TCP
TargetPort:        8080/TCP #This is wrong
Endpoints:         10.244.2.7:8080
Session Affinity:  None
Events:            <none>
```

### Troubleshooting Test 3

**The same 2 tier application is deployed in the gamma namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue**

`$ kubectl get all -n gamma`

`$ kubectl describe svc mysql-service -n gamma`

```yaml
Name:              mysql-service
Namespace:         gamma
Labels:            <none>
Annotations:       <none>
Selector:          name=sql00001 #selector is wrong
Type:              ClusterIP
IP:                10.99.208.191
Port:              <unset>  3306/TCP
TargetPort:        3306/TCP
Endpoints:         <none>
Session Affinity:  None
Events:            <none>
```

### Troubleshooting Test 4

**The same 2 tier application is deployed in the delta namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.**

`$ kubectl get all -n delta`

`$ kubectl describe deployment -n delta webapp-mysql`

```yaml
Pod Template:
  Labels:  name=webapp-mysql
  Containers:
   webapp-mysql:
    Image:      mmumshad/simple-webapp-mysql
    Port:       8080/TCP
    Host Port:  0/TCP
    Environment:
      DB_Host:      mysql-service
      DB_User:      sql-user # DB_User is wrong
      DB_Password:  paswrd
```

`$ kubectl edit deployment -n delta webapp-mysql`

### Troubleshooting Test 5

**The same 2 tier application is deployed in the epsilon namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.**

`$ kubectl get all -n epsilon`

`$ kubectl describe pod mysql -n epsilon`

```yaml
Containers:
  mysql:
    Container ID:   docker://3216a5ae89df3f25f5de57dbc956759521884c771144e13c46357caa9fb49bdc
    Image:          mysql:5.6
    Image ID:       docker-pullable://mysql@sha256:60c27b50ca72d81d92a743a965a82f124a4e123c7d374a021887286408878d60
    Port:           3306/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Wed, 22 Jul 2020 10:38:08 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      MYSQL_ROOT_PASSWORD:  passwooooorrddd #paswrd
```

```bash
$ kubectl edit pod mysql -n epsilon
```

`$ kubectl describe deployment -n epsilon webapp-mysql`

```yaml
8080/TCP
    Host Port:  0/TCP
    Environment:
      DB_Host:      mysql-service
      DB_User:      sql-user #root
      DB_Password:  paswrd
    Mounts:         <none>
  Volumes:          <none>
```

`$ kubectl edit deployment -n epsilon webapp-mysql`

### Troubleshooting Test 6

**The same 2 tier application is deployed in the zeta namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.**

`$ kubectl get all -n zeta`

```bash
service/web-service     NodePort    10.96.21.191     <none>        8080:30088/TCP   57s
# port should be 30081
```

`$ kubectl edit svc -n zeta web-service`

`$ kubectl describe pod -n zeta mysql`

```yaml
Host Port:      0/TCP
State:          Running
  Started:      Wed, 22 Jul 2020 10:48:23 +0000
Ready:          True
Restart Count:  0
Environment:
  MYSQL_ROOT_PASSWORD:  passwooooorrddd #paswrd
```

`$ kubectl describe deployment -n zeta webapp-mysql`

```yaml
Pod Template:
  Labels:  name=webapp-mysql
  Containers:
   webapp-mysql:
    Image:      mmumshad/simple-webapp-mysql
    Port:       8080/TCP
    Host Port:  0/TCP
    Environment:
      DB_Host:      mysql-service
      DB_User:      sql-user #root
      DB_Password:  paswrd
```
