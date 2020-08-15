# Install Prometheus
```
helm install --name prometheus stable/prometheus
```

# Access Prometheus
```
export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")

kubectl --namespace default port-forward $POD_NAME 9090
```

# Install Grafana
```
helm search grafana

helm install --name grafana stable/grafana
```

# Access Grafana
```
export POD_NAME=$(kubectl get pods --namespace default -l "app=grafana" -o jsonpath="{.items[0].metadata.name}")

kubectl --namespace default port-forward $POD_NAME 3000

kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
RandomPassword
```





