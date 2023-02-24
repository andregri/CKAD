# Ingress

Install the Nginx ingress controller following the [official guide](https://kubernetes.github.io/ingress-nginx/deploy/#quick-start).

## Deploy

Create the resources:

```bash
kubectl apply -f frontend-pod.yml
kubectl apply -f frontend-svc.yml
kubectl apply -f ingress.yml
```

Forward a local port to the ingress controller:

```bash
kubectl port-forward --namespace=ingress-nginx service/ingress-nginx-controller 8080:80
```

At this point, if you access http://myapp.localdev.me:8080/, you should see NGINX default page.

Note that localdev.me points to localhost! See [here](https://mxtoolbox.com/SuperTool.aspx?action=a%3ademo.localdev.me&run=toolpage).

## What I've learned

Check that the ClusterIP service is attached to the correct pods.
Do `kubectl describe svc <svc-name>` to check that `Endpoints` is not empty or populated with wrong values.

localdev.me points to localhost so you don't need to add a line to `/etc/hosts` file.