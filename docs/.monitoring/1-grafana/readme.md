### Deploying grafana via grafana-cluster operator

#### Prerequisites
- you need to have a postgresql database setup in order to deploy grafana in HA mode
- the postgresql in this setup needs Crunchy Postgres Operator > v5.x.x installed in the cluster
- connection details need to be edited in __1-grafana.yml__ file after creation of the grafana-db postgres cluster
- you need to have created namespace infrastructure (kubectl create ns infrastructure)

#### Installation
```shell
# Namespace 'infrastructure' is required

# install grafana operator
kubectl apply -f operator/<version>/

# create a grafana postgres cluster
kubectl apply -f 0-grafana-db.yml 

# install grafana
kubectl apply -f 1-grafana.yml

# setup prometheus datasaource
kubectl apply -f 2-prometheus-datasaource.yml
```

### Accessing grafana
- once grafana is up and running credentials are obtained inspecting __grafana-admin-credentials__ secret which is automatically created by grafana deployment by operator
```shell
kubectl get secret grafana-admin-credentials --template='{{.data.GF_SECURITY_ADMIN_USER | base64decode}} / {{.data.GF_SECURITY_ADMIN_PASSWORD | base64decode}}'
```

- in order to access grafana, a port-forward needs to happen
```shell
kubectl port-forward svc/grafana-service 3000:3000
```

- grafana operator supports custom CRDs for dashboards - GrafanaDashboard - and for datasources - GrafanaDatasource
- example dashboard
```yaml
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDashboard
metadata:
  name: elasticsearch-cluster
  namespace: infrastructure
  labels:
    app: grafana
spec:
  customFolderName: "infrastructure"
  url: https://grafana.com/api/dashboards/6483/revisions/2/download
  datasources:
    - inputName: "DS_PROMETHEUS"
      datasourceName: "Prometheus"
```
 - example datasource
```yaml
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDataSource
metadata:
  name: prometheus-datasource
spec:
  name: middleware.yaml
  datasources:
    - name: Prometheus
      type: prometheus
      access: proxy
      url: http://prometheus-operated:9090
      isDefault: true
      version: 1
      editable: true
      jsonData:
        tlsSkipVerify: false
        timeInterval: "15s"
```
