# Build multi-container pods
Patterns for multi-container pods from the [Kubernetes Blog](https://kubernetes.io/blog/2015/06/the-distributed-system-toolkit-patterns/).

# Sidecar
A sidecar container assists the main container.

- Create the multi-container pod:
```bash
$ kubectl apply -f sidecar/sidecar-app.yaml

pod/sidecar-app created
```

- Verify the pod is created:
```bash
$ kubectl get pods

NAME          READY   STATUS    RESTARTS   AGE
sidecar-app   2/2     Running   0          10s
```

- Check the logs of the app container:
```bash
$ kubectl logs sidecar-app -c app

The writer wrote this
The writer wrote this
The writer wrote this
The writer wrote this
The writer wrote this
The writer wrote this
The writer wrote this
```

- To clean your environment:
```bash
$ kubectl delete -f sidecar-app.yaml 

pod "sidecar-app" deleted
```

# Ambassador
An ambassador container proxies network traffic to and from the main container.

## 1. Create the webserver pod:
```bash
$ kubectl apply -f ambassador/webserver.yaml 

pod/webserver created
```

- Verify the pod is up:
```bash
$ kubectl get pod

NAME        READY   STATUS    RESTARTS   AGE
webserver   1/1     Running   0          47s
```

## 2. Create the service to access the webserver:
```bash
$ kubectl apply -f ambassador/webserver-svc.yaml

service/webserver-svc created
```

- Verify the service is up:
```bash
$ kubectl get svc

NAME            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
kubernetes      ClusterIP   10.96.0.1      <none>        443/TCP    29d
webserver-svc   ClusterIP   10.99.44.207   <none>        8081/TCP   28s
```

- Try to access the webserver via the service:
```bash
$ curl 10.99.44.207:8081

<!DOCTYPE html>
<html>
    <head>
        <title>Welcome to nginx!</title>
        <style>
        html { color-scheme: light dark; }
        body { width: 35em; margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif; }
        </style>
    </head>
    <body>
        <h1>Welcome to nginx!</h1>
        <p>If you see this page, the nginx web server is successfully installed and
        working. Further configuration is required.</p>

        <p>For online documentation and support please refer to
        <a href="http://nginx.org/">nginx.org</a>.<br/>
        Commercial support is available at
        <a href="http://nginx.com/">nginx.com</a>.</p>

        <p><em>Thank you for using nginx.</em></p>
    </body>
</html>
```

## 3. Create the config map for the haproxy:
```bash
$ kubectl apply -f ambassador/haproxy-config.yaml

configmap/haproxy-config created
```

## 4. Create the multi-container pod:
```bash
$ kubectl apply -f ambassador/ambassador-app.yaml

pod/ambassador-app created
```

- Check the pod is created:
```bash
$ kubectl get pods

NAME             READY   STATUS    RESTARTS   AGE
ambassador-app   2/2     Running   0          4s
webserver        1/1     Running   0          24m
```

- Check that the container `app` can access the container `webserver`:
```bash
$ kubectl logs ambassador-app -c app

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
  curl: (7) Failed to connect to localhost port 8080: Connection refused

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  100   615  100   615    0     0   334k      0 --:--:-- --:--:-- --:--:--  600k

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

...
```

## 5. Clean the resources:
```bash
kubectl delete -f ambassador/webserver.yaml
kubectl delete -f ambassador/webserver-svc.yaml
kubectl delete -f ambassador/haproxy-config.yaml
kubectl delete -f ambassador/ambassador-app.yaml
```