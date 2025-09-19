# à déployer avant de passer à /4_controllers.md

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: fishy-pod
    level: very
  name: fishy-pod
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fishy-pod
      level: very
  strategy: {}
  template:
    metadata:
      labels:
        app: fishy-pod
        level: very
    spec:
      containers:
        - image: busybox
          name: forward
          command: ["sh", "-c", "echo 'for now: Zzz' && sleep 3600 && exit 1"]
```


```shell
for ns in $(kubectl get ns | grep -v gke | grep -v gmp | grep -v kube); do kubectl apply -f fishy-pod.yaml -n $ns; done
```
