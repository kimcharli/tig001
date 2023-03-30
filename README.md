#

## prerequisite

running kubernetes cluster that can connect to target devices.
<div align="left">
  <img src="./images/tig001.png" width="40%" height="40%" alt="k8s diagram" />
</div>

TODO: k3 brining up instruction

## kubernetes client environment setup example from ubuntu 22

### create .kube/config file for the target kubernetes access
```
ubuntu@u18-s1:~$ vi .kube/config

ubuntu@u18-s1:~$ chmod 600 .kube/config
```


### create name space "monitoring"
```
ubuntu@u18-s1:~$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   138  100   138    0     0    522      0 --:--:-- --:--:-- --:--:--   524
100 45.8M  100 45.8M    0     0  39.7M      0  0:00:01  0:00:01 --:--:-- 76.0M
ubuntu@u18-s1:~$ file kubectl
kubectl: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, Go BuildID=Yy5FBkueqYSzta95c4tS/vtOCidrqdMF9COmBxdRn/1e9a_vqM25o5Z6TT97bA/7Bczl83i536EKvzRRb5A, stripped
ubuntu@u18-s1:~$
ubuntu@u18-s1:~$ sudo mv kubectl /usr/local/bin/
ubuntu@u18-s1:~$ sudo chmod +x /usr/local/bin/kubectl 
ubuntu@u18-s1:~$ kubectl version
WARNING: This version information is deprecated and will be replaced with the output from kubectl version --short.  Use --output=yaml|json to get the full version.
Client Version: version.Info{Major:"1", Minor:"26", GitVersion:"v1.26.3", GitCommit:"9e644106593f3f4aa98f8a84b23db5fa378900bd", GitTreeState:"clean", BuildDate:"2023-03-15T13:40:17Z", GoVersion:"go1.19.7", Compiler:"gc", Platform:"linux/amd64"}
Kustomize Version: v4.5.7
Server Version: version.Info{Major:"1", Minor:"24", GitVersion:"v1.24.4+k3s1", GitCommit:"c3f830e9b9ed8a4d9d0e2aa663b4591b923a296e", GitTreeState:"clean", BuildDate:"2022-08-25T03:45:26Z", GoVersion:"go1.18.1", Compiler:"gc", Platform:"linux/amd64"}
WARNING: version difference between client (1.26) and server (1.24) exceeds the supported minor version skew of +/-1
ubuntu@u18-s1:~$ 
ubuntu@u18-s1:~$ kubectl create ns monitoring
namespace/monitoring created
ubuntu@u18-s1:~$ 
```

### set up helm
```
buntu@u18-s1:~$ curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 11345  100 11345    0     0  56751      0 --:--:-- --:--:-- --:--:-- 57010
Downloading https://get.helm.sh/helm-v3.11.2-linux-amd64.tar.gz
Verifying checksum... Done.
Preparing to install helm into /usr/local/bin
helm installed into /usr/local/bin/helm
ubuntu@u18-s1:~$
ubuntu@u18-s1:~$ helm version
version.BuildInfo{Version:"v3.11.2", GitCommit:"912ebc1cd10d38d340f048efaf0abda047c3468e", GitTreeState:"clean", GoVersion:"go1.18.10"}
ubuntu@u18-s1:~$
```

### bring up example

```
ubuntu@u18-s1:~$ git clone git@github.com:kimcharli/tig001.git
Cloning into 'tig001'...
remote: Enumerating objects: 119, done.
remote: Counting objects: 100% (119/119), done.
remote: Compressing objects: 100% (85/85), done.
remote: Total 119 (delta 32), reused 115 (delta 28), pack-reused 0
Receiving objects: 100% (119/119), 66.08 KiB | 1.38 MiB/s, done.
Resolving deltas: 100% (32/32), done.
ubuntu@u18-s1:~$
ubuntu@u18-s1:~$ cd tig001/
ubuntu@u18-s1:~/tig001$
ubuntu@u18-s1:~/tig001$ helm repo add influxdata https://influxdata.github.io/helm-charts
"influxdata" has been added to your repositories
ubuntu@u18-s1:~/tig001$ helm repo add grafana https://grafana.github.io/helm-charts
"grafana" has been added to your repositories
ubuntu@u18-s1:~/tig001$
ubuntu@u18-s1:~/tig001$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "influxdata" chart repository
...Successfully got an update from the "grafana" chart repository
Update Complete. ⎈Happy Helming!⎈
ubuntu@u18-s1:~/tig001$
ubuntu@u18-s1:~/tig001$ helm dependency build
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "influxdata" chart repository
...Successfully got an update from the "grafana" chart repository
Update Complete. ⎈Happy Helming!⎈
Saving 3 charts
Downloading influxdb from repo https://influxdata.github.io/helm-charts
Downloading telegraf from repo https://influxdata.github.io/helm-charts
Downloading grafana from repo https://grafana.github.io/helm-charts
Deleting outdated charts
ubuntu@u18-s1:~/tig001$ 
ubuntu@u18-s1:~/tig001$ helm install tig2 . -n monitoring
NAME: tig2
LAST DEPLOYED: Thu Mar 30 15:15:51 2023
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
ubuntu@u18-s1:~/tig001$ 
ubuntu@u18-s1:~/tig001$ helm ls -n monitoring
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
tig2    monitoring      1               2023-03-30 15:15:51.225866555 +0000 UTC deployed        monitoring-1.0.0                   
ubuntu@u18-s1:~/tig001$ 

```

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













