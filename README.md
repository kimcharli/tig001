#

Based on helm v3


## bring up

```
helm repo add influxdata https://influxdata.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

kubectl create ns monitoring

helm dependency build

```

Adjust values.yaml before proceeding. Then start chart.
```
helm install tig2 . -n monitoring
```

An example.
```
kim@ckim-mbp tig001 % helm install tig2 . -n monitoring

NAME: tig2
LAST DEPLOYED: Wed Mar 29 22:05:35 2023
NAMESPACE: monitoring
STATUS: deployed
REVISION: 1
NOTES:
Thank you for installing monitoring.

Your release is named tig2.

To learn more about the release, try:

  $ helm status tig2
  $ helm get all tig2
  $ kubectl get pod -n monitoring

export SERVICE_IP=$(kubectl get svc -n monitoring tig2-grafana -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
 
Grafana
  URL: http://$SERVICE_IP:3000/
  user: admin
  password: contrail123
  influxDb: http://tig2-influxdb:8086
  database: telegraf
```

## debug

```
helm install --generate-name --dry-run --debug charts/telegraf -n monitoring

helm status tig2

helm show chart influxdata/influxdb

helm template influxdata/influxdb
```

The telegraf pod need to bounce to workaround the timing issue.

```
kubectl delete pod -n monitoring -l app.kubernetes.io/name=telegraf
```


## grafana

## junos config

- gRPC port 32767 was configured to work with values.yaml telegraf.config.inputs[jti_openconfig_telemetry].servers
- route 10.42.0.0/16 was for return traffic. The service cidr is not likely to be the same as infra routing.
```
system {
    services {
        extension-service {
            request-response {
                grpc {
                    clear-text {
                        port 32767;
                    }
                    routing-instance mgmt_junos;
                    skip-authentication;
                }
            }
            notification {              
                allow-clients {         
                    address 0.0.0.0/0;  
                }                       
            }                           
        }                
    }
}
routing-instances {
    mgmt_junos {
        routing-options {
            static {
                route 10.42.0.0/16 next-hop 10.85.192.10;
            }
        }
    }
}
```

## TODO

- use influxDB v2













