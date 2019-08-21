# Introduction

Helm chart for deploying [flask example application](https://github.com/mrx88/aks-deploy-example/tree/master/app) to Azure Kubernetes Service cluster.

# Verify Chart

```

# Check if linting is OK
helm lint .

==> Linting .
[INFO] Chart.yaml: icon is recommended

1 chart(s) linted, no failures

# Check if values are added correctly to templates
helm template .

---
# Source: martin-api/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: RELEASE-NAME-martin-api
  labels:
    app.kubernetes.io/name: martin-api
    helm.sh/chart: martin-api-3.0.0
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/managed-by: Tiller
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 5000
      protocol: TCP
      name: http
  selector:
    app.kubernetes.io/name: martin-api
    app.kubernetes.io/instance: RELEASE-NAME

---
# Source: martin-api/templates/deployment.yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: RELEASE-NAME-martin-api
  labels:
    app.kubernetes.io/name: martin-api
    helm.sh/chart: martin-api-3.0.0
    app.kubernetes.io/instance: RELEASE-NAME
    app.kubernetes.io/managed-by: Tiller
spec:
  replicas: 10
  selector:
    matchLabels:
      app.kubernetes.io/name: martin-api
      app.kubernetes.io/instance: RELEASE-NAME
  template:
    metadata:
      labels:
        app.kubernetes.io/name: martin-api
        app.kubernetes.io/instance: RELEASE-NAME
    spec:
      containers:
        - name: martin-api
          image: "martinacrdev.azurecr.io/martin-api-alpine:v3"
          imagePullPolicy: Always
          restartPolicy: Always
          resources:
            {}
```

# Deployment

## Install the Chart
```

 (⎈ MartinAKScluster-admin:default) helm install .
NAME:   youngling-wolverine
LAST DEPLOYED: Wed Aug 21 15:12:48 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Service
NAME                            AGE
youngling-wolverine-martin-api  0s

==> v1beta1/Deployment
youngling-wolverine-martin-api  0s

==> v1/Pod(related)

NAME                                            READY  STATUS             RESTARTS  AGE
youngling-wolverine-martin-api-9cd4bff8f-8m2ls  0/1    ContainerCreating  0         0s


NOTES:
1. Get the application URL by running these commands:
     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch the status of by running 'kubectl get svc -w youngling-wolverine-martin-api'
  export SERVICE_IP=$(kubectl get svc --namespace default youngling-wolverine-martin-api -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
  echo http://$SERVICE_IP:80


```

## List Releases

```
 (⎈ MartinAKScluster-admin:default) helm list
NAME                    REVISION        UPDATED                         STATUS          CHART                   APP VERSION     NAMESPACE
promaks                 1               Sun Aug 11 16:27:45 2019        DEPLOYED        prometheus-8.15.0       2.11.1          default
youngling-wolverine     3               Wed Aug 21 15:21:22 2019        DEPLOYED        martin-api-3.0.0        1.0             default
```
## Upgrade the Chart Version

Modify Chart.yaml accordingly. I've increased replicas count and version number.

```
(⎈ MartinAKScluster-admin:default) helm upgrade youngling-wolverine .
Release "youngling-wolverine" has been upgraded. Happy Helming!
LAST DEPLOYED: Wed Aug 21 15:19:39 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Service
NAME                            AGE
youngling-wolverine-martin-api  6m

==> v1beta1/Deployment
youngling-wolverine-martin-api  6m

==> v1/Pod(related)

NAME                                            READY  STATUS             RESTARTS  AGE
youngling-wolverine-martin-api-9cd4bff8f-6crd4  0/1    ContainerCreating  0         0s
youngling-wolverine-martin-api-9cd4bff8f-6g2pk  0/1    Pending            0         0s
youngling-wolverine-martin-api-9cd4bff8f-88gwr  0/1    Pending            0         0s
youngling-wolverine-martin-api-9cd4bff8f-8m2ls  1/1    Running            0         6m
youngling-wolverine-martin-api-9cd4bff8f-8mkwq  0/1    Pending            0         0s
youngling-wolverine-martin-api-9cd4bff8f-b58ws  0/1    ContainerCreating  0         0s
youngling-wolverine-martin-api-9cd4bff8f-mpxj2  0/1    Pending            0         0s
youngling-wolverine-martin-api-9cd4bff8f-xrgqb  0/1    Pending            0         0s
youngling-wolverine-martin-api-9cd4bff8f-znz2t  0/1    ContainerCreating  0         0s
youngling-wolverine-martin-api-9cd4bff8f-zzbtq  0/1    ContainerCreating  0         0s


NOTES:
1. Get the application URL by running these commands:
     NOTE: It may take a few minutes for the LoadBalancer IP to be available.
           You can watch the status of by running 'kubectl get svc -w youngling-wolverine-martin-api'
  export SERVICE_IP=$(kubectl get svc --namespace default youngling-wolverine-martin-api -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
  echo http://$SERVICE_IP:80

  ```

# Rollback the Chat version

```

# Rollback to previous version (0 stands for previous version, otherwise revision number)
helm rollback youngling-wolverine 0
Rollback was a success! Happy Helming!

# Verify if pods go back to previous state:

(⎈ MartinAKScluster-admin:default) kubectl get pods                   
NAME                                                     READY   STATUS        RESTARTS   AGE
promaks-prometheus-alertmanager-66c54f6757-hhhnm         2/2     Running       0          9d
promaks-prometheus-kube-state-metrics-788c6b7964-z9mgw   1/1     Running       0          9d
promaks-prometheus-node-exporter-26ttj                   1/1     Running       0          9d
promaks-prometheus-node-exporter-56t4c                   1/1     Running       0          9d
promaks-prometheus-pushgateway-794fb945d8-6kl6g          1/1     Running       0          9d
promaks-prometheus-server-bbd666f5f-m9vlj                2/2     Running       0          9d
youngling-wolverine-martin-api-9cd4bff8f-6crd4           0/1     Terminating   0          106s
youngling-wolverine-martin-api-9cd4bff8f-6g2pk           0/1     Terminating   0          106s
youngling-wolverine-martin-api-9cd4bff8f-88gwr           0/1     Terminating   0          106s
youngling-wolverine-martin-api-9cd4bff8f-8m2ls           1/1     Running       0          8m37s
youngling-wolverine-martin-api-9cd4bff8f-8mkwq           0/1     Terminating   0          106s
youngling-wolverine-martin-api-9cd4bff8f-b58ws           0/1     Terminating   0          106s
youngling-wolverine-martin-api-9cd4bff8f-mpxj2           0/1     Terminating   0          106s
youngling-wolverine-martin-api-9cd4bff8f-xrgqb           0/1     Terminating   0          106s
youngling-wolverine-martin-api-9cd4bff8f-znz2t           0/1     Terminating   0          106s
```

# Verify Service

```
(⎈ MartinAKScluster-admin:default) curl http://$(kubectl get svc --namespace default youngling-wolverine-martin-api -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
Test API index page%
```

# Delete the Chart

```
(⎈ MartinAKScluster-admin:default) helm delete youngling-wolverine
release "youngling-wolverine" deleted
```