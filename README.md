# Despliega tus aplicaciones en Kubernetes con Helm (III)

Repositorio de código del artículo de [enmilocalfunciona.io](https://enmilocalfunciona.io/despliega-tus-aplicaciones-en-kubernetes-con-helm-iii)

## Voting app

The folder k8s-specs contains the yaml specifications of the Voting App's services.

First create the voting-app namespace

```sh
$ kubectl create namespace voting-app
```

Run the following command to create the deployments and services objects:
```sh

$ kubectl create -f k8s-specs/ -n voting-app

deployment "db" created
service "db" created
deployment "redis" created
service "redis" created
deployment "result" created
service "result" created
deployment "vote" created
service "vote" created
deployment "worker" created
```

The vote interface is then available on port 31000 on each host of the cluster, the result one is available on port 31001.

### Architecture

![Architecture](https://github.com/dockersamples/example-voting-app/raw/master/architecture.png)

## Voting App Helm

```sh
helm upgrade --install <your-chart-name> voting-app-enmilocalfunciona --namespace voting-app
```

## Chart packaging

### helm lint

```sh
helm lint .
...
==> Linting .
Lint OK

1 chart(s) linted, no failures
```

### chart packaging

```sh
helm package .

...
Successfully packaged chart and saved it to: .../enmilocalfunciona-voting-app/enmilocalfunciona-voting-app-0.1.0.tgz
```

### Enable NodePort and API access on chart museum

```sh
helm upgrade --install chart-museum stable/chartmuseum --set service.type=NodePort,service.nodePort=32000,env.open.DISABLE_API=false --namespace chart-museum
```

### Upload chart

```sh
curl -L --data-binary "@enmilocalfunciona-voting-app-0.1.0.tgz" $(minikube ip):32000/apis/charts

{"saved":true}%
```