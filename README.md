# Monitoring

With Helm we will install Prometheus

[Docs link](https://artifacthub.io/packages/helm/prometheus-community/prometheus)

Also, I change default volumes to (my-values.yaml) 

```
server:
  persistentVolume:
    enabled: false  

alertmanager:
  persistentVolume:
    enabled: false  
```

And install by this:

```
helm install my-prometheus prometheus-community/prometheus --version 25.8.0 -f my-values.yaml
```

For using my URL in the browser I create ingress and use my nginx-ingress on cluster: (templates/ingress) 

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-prometheus-ingress
  namespace: default
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  ingressClassName: nginx
  rules:
  - host: prometheus.mari1327.pp.ua
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: heml-app-service
            port:
              number: 9090
```

And expose port: 

``
kubectl expose service my-prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-ext
``



For grafana: 

```
helm repo add grafana https://grafana.github.io/helm-charts 
helm repo update
helm install grafana grafana/grafana
```

By default our user is admin and the password we can get by:

```
kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

Also apply ingress for grafana.mari1327.pp.ua (templates/grafana-ingress.yaml)




