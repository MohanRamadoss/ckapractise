## Prometheus Architecture
![](1.jpg)

## Client Libraries
![](2.jpg)

- Prometheus supported libraries:

    Go
    Java or Scala
    Python
    Ruby
-Third-party libraries:

    Bash
    C++
    Common Lisp
    Elixir
    Erlang
    Haskell
    Lua for Nginx
    Lua for Tarantool
    .NET / C#
    Node.js
    Perl
    PHP
    Rust

This is the code used to instrument the app using the NodeJS library prom-client:

    var Register = require('prom-client').register;
    var Counter = require('prom-client').Counter;
    var Histogram = require('prom-client').Histogram;
    var Summary = require('prom-client').Summary;
    var ResponseTime = require('response-time');


    module.exports.totalNumOfRequests = totalNumOfRequests = new Counter({
        name: 'totalNumOfRequests',
        help: 'Total number of requests made',
        labelNames: ['method']
    });

    module.exports.pathsTaken = pathsTaken = new Counter({
        name: 'pathsTaken',
        help: 'Paths taken in the app',
        labelNames: ['path']
    });

    module.exports.responses = responses = new Summary({
        name: 'responses',
        help: 'Response time in millis',
        labelNames: ['method', 'path', 'status']
    });

    module.exports.startCollection = function () {
        require('prom-client').collectDefaultMetrics();
    };

    module.exports.requestCounters = function (req, res, next) {
        if (req.path != '/metrics') {
            totalNumOfRequests.inc({ method: req.method });
            pathsTaken.inc({ path: req.path });
        }
        next();
    }

    module.exports.responseCounters = ResponseTime(function (req, res, time) {
        if(req.url != '/metrics') {
            responses.labels(req.method, req.url, res.statusCode).observe(time);
        }
    })

    module.exports.injectMetricsRoute = function (App) {
        App.get('/metrics', (req, res) => {
            res.set('Content-Type', Register.contentType);
            res.end(Register.metrics());
        });
    };

## Exporters
![](3.jpg)

- Exporters are software that is deployed next to the application that you want to have metrics collected from. Instrumentation for exporters are known as custom collectors or ConstMetrics.

How exporters work:

    Takes requests
    Gathers the data
    Formats the data
    Returns the data to Prometheus
Databases:

    Consul exporter
    Memcached exporter
    MySQL server exporter
Hardware:

    Node/system metrics exporter
HTTP:

    HAProxy exporter
Other monitoring systems:

    AWS CloudWatch exporter
    Collectd exporter
    Graphite exporter
    InfluxDB exporter
    JMX exporter
    SNMP exporter
    StatsD exporter
Miscellaneous:

    Blackbox exporter

## Service Discovery

![](4.jpg)

## Scraping

- Prometheus is a Pull based system.
- Prometheus uses the HTTP endpoints defined in it's configuration to pull the metrics from target systems.

## Setting up Prometheus
- What are we going to setup?

![](5.jpg)

- A metrics namespace for our environment to live in
- A ClusterRole to give Prometheus access to targets using Service Discovery
- A ConfigMap map that will be used to generate the Prometheus config file
- A Prometheus Deployment and Service Kube State Metrics to get access to metrics on the Kubernetes API

- Create a file called namespaces.yml. This file will be used to create the monitoring namespace.
namespaces.yml

        {
        "kind": "Namespace",
        "apiVersion": "v1",
        "metadata": {
            "name": "monitoring",
            "labels": {
            "name": "monitoring"
            }
        }
        }

- Apply the namespace:

        kubectl apply -f namespaces.yml
- Create a file called clusterRole.yml. This will be used to set up the cluster's roles.

```
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: prometheus
rules:
- apiGroups: [""]
  resources:
  - nodes
  - nodes/proxy
  - services
  - endpoints
  - pods
  verbs: ["get", "list", "watch"]
- apiGroups:
  - extensions
  resources:
  - ingresses
  verbs: ["get", "list", "watch"]
- nonResourceURLs: ["/metrics"]
  verbs: ["get"]
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: prometheus
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: prometheus
subjects:
- kind: ServiceAccount
  name: default
  namespace: monitoring
```
- Apply the cluster roles to the Kubernetes cluster:

        kubectl apply -f clusterRole.yml
- Create config-map.yml. Kubernetes will use this file to manage the prometheus.yml configuration file. **Replace `KUBERNETES_IP` in the below config with the private IP of the Master and worker nodes respectively**.

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-server-conf
  labels:
    name: prometheus-server-conf
  namespace: monitoring
data:
  prometheus.yml: |-
    global:
      scrape_interval: 5s
      evaluation_interval: 5s

    scrape_configs:
      - job_name: 'node-exporter'
        static_configs:
        - targets: ['<KUBERNETES_IP>:9100', '<KUBERNETES_IP>:9100']

      - job_name: 'kubernetes-apiservers'

        kubernetes_sd_configs:
        - role: endpoints
        scheme: https

        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token

        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'

        scheme: https

        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token

        kubernetes_sd_configs:
        - role: node

        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics


      - job_name: 'kubernetes-pods'

        kubernetes_sd_configs:
        - role: pod

        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - action: labelmap
          regex: __meta_kubernetes_pod_label_(.+)
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: kubernetes_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: kubernetes_pod_name

      - job_name: 'kubernetes-cadvisor'

        scheme: https

        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token

        kubernetes_sd_configs:
        - role: node

        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor

      - job_name: 'kubernetes-service-endpoints'

        kubernetes_sd_configs:
        - role: endpoints

        relabel_configs:
        - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scheme]
          action: replace
          target_label: __scheme__
          regex: (https?)
        - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_service_annotation_prometheus_io_port]
          action: replace
          target_label: __address__
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
        - action: labelmap
          regex: __meta_kubernetes_service_label_(.+)
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: kubernetes_namespace
        - source_labels: [__meta_kubernetes_service_name]
          action: replace
          target_label: kubernetes_name
```
- Create the ConfigMap:

        kubectl apply -f config-map.yml
- Create prometheus-deployment.yml. This file will be used to create the Prometheus deployment; which will include the pods, replica sets and volumes.

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: prometheus-deployment
  namespace: monitoring
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: prometheus-server
    spec:
      containers:
        - name: prometheus
          image: prom/prometheus:v2.2.1
          args:
            - "--config.file=/etc/prometheus/prometheus.yml"
            - "--storage.tsdb.path=/prometheus/"
            - "--web.enable-lifecycle"
          ports:
            - containerPort: 9090
          volumeMounts:
            - name: prometheus-config-volume
              mountPath: /etc/prometheus/
            - name: prometheus-storage-volume
              mountPath: /prometheus/
        - name: watch
          image: weaveworks/watch:master-5b2a6e5
          imagePullPolicy: IfNotPresent
          args: ["-v", "-t", "-p=/etc/prometheus", "-p=/var/prometheus", "curl", "-X", "POST", "--fail", "-o", "-", "-sS", "http://localhost:9090/-/reload"]
          volumeMounts:
            - name: prometheus-config-volume
              mountPath: /etc/prometheus
      volumes:
        - name: prometheus-config-volume
          configMap:
            defaultMode: 420
            name: prometheus-server-conf

        - name: prometheus-storage-volume
          emptyDir: {}
```
- Deploy the Prometheus environment:

        kubectl apply -f prometheus-deployment.yml
- Finally, we will finish off the Prometheus environment by creating a server to make publicly accessible. Create prometheus-service.yml.
```
apiVersion: v1
kind: Service
metadata:
  name: prometheus-service
  namespace: monitoring
  annotations:
      prometheus.io/scrape: 'true'
      prometheus.io/port:   '9090'

spec:
  selector:
    app: prometheus-server
  type: NodePort
  ports:
    - port: 8080
      targetPort: 9090
      nodePort: 8080
```
- Create the service that will make Prometheus publicly accessible:

        kubectl apply -f prometheus-service.yml
- Create the Kube State Metrics pod to get access to metrics on the Kubernetes API:
`kube-state-metrics.yml`:
```
apiVersion: v1
kind: Service
metadata:
  name: kube-state-metrics
  namespace: monitoring
  labels:
    app: kube-state-metrics
  annotations:
    prometheus.io/scrape: 'true'
spec:
  ports:
  - name: metrics
    port: 8080
    targetPort: metrics
    protocol: TCP
  selector:
    app: kube-state-metrics
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: kube-state-metrics
  namespace: monitoring
  labels:
    app: kube-state-metrics
spec:
  replicas: 1
  template:
    metadata:
      name: kube-state-metrics-main
      labels:
        app: kube-state-metrics
    spec:
      containers:
        - name: kube-state-metrics
          image: quay.io/coreos/kube-state-metrics:latest
          ports:
          - containerPort: 8080
            name: metrics
```
**Access Prometheus by visiting https://<MASTER_IP>:8080**

# Setting up Grafana
Create `grafana-deployment.yml`. This file will be used to create the Grafana deployment. Be sure to change the password.

grafana-deployment.yml:
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: grafana
  namespace: monitoring
  labels:
    app: grafana
    component: core
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: grafana
        component: core
    spec:
      containers:
        - image: grafana/grafana:3.1.1
          name: grafana
          env:
            - name: GF_SECURITY_ADMIN_PASSWORD
              value: password
          ports:
            - containerPort: 3000
          volumeMounts:
          - name: grafana-persistent-storage
            mountPath: /var
      volumes:
      - name: grafana-persistent-storage
        emptyDir: {}
```
Deploy Grafana:

`kubectl apply -f grafana-deployment.yml`
Crate `grafana-service.yml`. This file will be used to make the pod publicly accessible.

grafana-service.yml:
```
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
  namespace: monitoring

spec:
  selector:
    app: grafana
  type: NodePort
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 8000
```
Create the Grafana service:

`kubectl apply -f grafana-service.yml`

# Node Exporter

Repeat these steps on both your master and worker nodes.

Create the Prometheus user:

`adduser prometheus`
Download Node Exporter:

  cd /home/prometheus
  curl -LO "https://github.com/prometheus/node_exporter/releases/download/v0.16.0/node_exporter-0.16.0.linux-amd64.tar.gz"
  tar -xvzf node_exporter-0.16.0.linux-amd64.tar.gz
  mv node_exporter-0.16.0.linux-amd64 node_exporter
  cd node_exporter
  chown prometheus:prometheus node_exporter
  vi /etc/systemd/system/node_exporter.service
/etc/systemd/system/node_exporter.service:
```
[Unit]
Description=Node Exporter

[Service]
User=prometheus
ExecStart=/home/prometheus/node_exporter/node_exporter

[Install]
WantedBy=default.target
```
Reload systemd:

```systemctl daemon-reload```
Enable the node_exporter service:

```systemctl enable node_exporter.service```
Start the node_exporter service:

```systemctl start node_exporter.service```

