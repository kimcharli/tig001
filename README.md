#


## monitoring


```
helm repo add influxdata https://helm.influxdata.com/
helm repo add grafana https://grafana.github.io/helm-charts

helm upgrade --install telegraf influxdata/telegraf

cd charts/tig2/chars
helm pull influxdata/telegraf --untar
helm pull influxdata/influxdb --untar
helm pull grafana/grafana --untar

```


```
kubectl create ns monitoring
helm install tig2 charts/tig2/ -n monitoring

helm delete tig2 -n monitoring

```

### grafana

Create / Data Sources / InfluxDB 
















