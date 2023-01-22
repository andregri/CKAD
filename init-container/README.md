# Init container

- Create the pod: the init container sleeps for 60 seconds, then the main container is created
```bash
$ kubectl apply -f pod.yaml

pod/init-test created
```

- Monitor the pod to check that :
```bash
$ kubectl get pod -w

NAME        READY   STATUS            RESTARTS   AGE
init-test   0/1     Init:0/1          0          7s
init-test   0/1     PodInitializing   0          63s
init-test   1/1     Running           0          67s
```