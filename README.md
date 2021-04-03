# TIG Stack


## k8s setup example

Create a cluster with k3s.
- A an example for ubuntu 18.04.
- eth0 ip address was 10.85.95.158
- k3s provides LoadBalancer and StorageClass by default
```
curl -sfL https://get.k3s.io | sudo sh -
mkdir .kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sed -i 's/127.0.0.1/10.85.95.158/' ~/.kube/config
snap install helm --classic
```


### grafana

Create / Data Sources / InfluxDB 


## Run helm chart

```
kubectl get nodes
git clone https://github.com/kimcharli/tig001
cd tig001

vi values.yaml   ### 

helm install tig2 . -n monitoring --create-namespace
```

Status Checking
```
helm ls -n monitoring
kubectl get pods -n monitoring
kubectl logs -f  -n monitoring tig2-telegraf-79c8b8c856-vnm9w
kubectl delete pod -n monitoring tig2-telegraf-79c8b8c856-vnm9w 

###when prawning telegraf and influxdb simultantiously, the telegraf pod will enter an error status as influxdb has not been ready yet. 
###further improvement needed to delay the prawning of telegraf pod (i.e. after influxdb is ready)

kubectl get pods -n monitoring
kubectl logs -f  -n monitoring tig2-telegraf-79c8b8c856-5vr99 
kubectl get svc -n monitoring
kubectl exec -it tig2-influxdb-0  -n monitoring -- bash

```

Delete the Release
```
helm delete tig2 -n monitoring
```

## Influxdb2

create Organization org1 with bucket bucket1




## Repo setup


```
helm repo add influxdata https://helm.influxdata.com/
helm repo add grafana https://grafana.github.io/helm-charts

helm upgrade --install telegraf influxdata/telegraf

helm pull influxdata/influxdb2 --untar -d charts
helm pull influxdata/telegraf --untar -d charts
helm pull influxdata/influxdb --untar -d charts
helm pull grafana/grafana --untar -d charts

```


```
kubectl create ns monitoring
helm install tig2 charts/tig2/ -n monitoring

helm delete tig2 -n monitoring

```












