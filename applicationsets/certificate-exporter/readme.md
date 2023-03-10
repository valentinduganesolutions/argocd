#### install x509-certificate-exporter
```shell
helm repo add enix https://charts.enix.io
helm install -n infrastructure x509-certificate-exporter enix/x509-certificate-exporter -f values.yml 
```

#### install certificate exporter dashboard
```shell
kubectl apply -f grafana-dashboard/0-certificate-exporter.yml
```