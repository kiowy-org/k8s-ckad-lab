# Solutions

Solutions du [TP aux environments en erreur](/8_debug.md)

## ERROR-01

Vous observer en utilisant `kubectl get pods -n error-01` que les pods sont au status `ImagePullBackOff`, annonçant un echec de récupération de l'image par le kubelet lors de la création du conteneur.
L'image utiliser dans ce namespace devrait être `gcr.io/kuar-demo/kuard-amd64:blue`. Confirmer l'erreur en observant les évènements via la commande: 
```shell
kubectl describe -n error-01 deploy kuard
```

**deployment.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kuard
spec:
  selector:
    matchLabels:
      app-of: error-01
  replicas: 2
  template:
    metadata:
      labels:
        app-of: error-01
    spec:
      containers:
        - image: gcr.io/kuar-demo/kuaed-amd64:blue
          name: kuard
          ports:
          - containerPort: 8080
            name: http
            protocol: TCP
```

> [!tip]
> Pour résoudre cette erreur, utilisez la commande `kubectl edit` sur l'object Deployment afin de l'image pour obtenir: `gcr.io/kuar-demo/kuard-amd64:blue`
> ```shell
> kubectl edit -n error-01 deploy kuard
> ```
> Vous pouvez aussi utiliser la commande `kubectl set image` vu en formation 🚀
> ```shell
> kubectl set image -n error-01 deploy kuard kuard=gcr.io/kuar-demo/kuard-amd64:blue
> ```


## ERROR-02
La communication avec l'application semble rompu ? regardons les manifestes du service et du déploiement.

**service.yaml**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: what-a-service
spec:
  selector:
    app-of: error-01
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```

**deployment.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kuard
spec:
  selector:
    matchLabels:
      app-of: error-02
  replicas: 2
  template:
    metadata:
      labels:
        app-of: error-02
    spec:
      containers:
        - image: gcr.io/kuar-demo/kuard-amd64:blue
          name: kuard
          ports:
          - containerPort: 8080
            name: http
            protocol: TCP
```

Le seul mécanisme de gourpage et de selection de Kubernetes étant les [labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/), c'est eux qui assure le lien entre un service et les pods qu'il exposent.

Ici, on peut voir que le label du pods (cf. `.spec.template.metadata.labels`) ne correspond pas à celui utilisé par le service pour selectionner les pods sur lesquels renvoyer les requêtes (cf. `.spec.selector`).

> [!tip]
> Pour résoudre cette erreur, utilisez la commande `kubectl edit` sur l'object Service afin de corriger le label utilisé par le selecteur `.spec.selector`
> ```shell
> kubectl edit -n error-02 svc what-a-service
> ```

## ERROR-03
Même retour ou constat que l'exercice précedent, la communication avec l'application déployée est rompu.

**service.yaml**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: what-a-service
spec:
  selector:
    app-of: error-03
  ports:
    - protocol: TCP
      port: 8090
      targetPort: 8090
```

**deployment.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kuard
spec:
  selector:
    matchLabels:
      app-of: error-03
  replicas: 3
  template:
    metadata:
      labels:
        app-of: error-03
    spec:
      containers:
        - image: gcr.io/kuar-demo/kuard-amd64:blue
          name: kuard
          ports:
          - containerPort: 8080
            name: http
            protocol: TCP
```

Aucun problème sur les labels dans cette environnement, néanmoins le port cible du service (cf. `.spec.ports.targetPorts`) et celui du pod (cf. `.spec.template.spec.containers.ports.containerPorts`) ne correspondent pas.
Le traffic est donc renvoyé sur le bon pod, mais sur le mauvais port !

> [!tip]
> Pour résoudre cette erreur, utilisez la commande `kubectl edit` sur l'object [Service](https://kubernetes.io/fr/docs/concepts/services-networking/service/) afin de corriger le port cible `.spec.ports.targetPorts`
> ```shell
> kubectl edit -n error-03 svc what-a-service
> ```

## ERROR-04

Via `kubectl get pods -n error-04` vous observez des pods en erreur avec le status `CreateContainerConfigError`. Accédez aux évènements de creation du conteneur via `kubectl get events -n error-04` ou via la commande 

```shell
kubectl describe -n error-04 deployment kuard
```

Vous constaterez l'erreur `can't find configmap: test`, indiquant que le kubelet ne trouve pas dans l'environnement (Kubernetes) l'objet [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/) attendu dans le manifest du conteneur.
Il n'y a actuellement pas de [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/) dans le namespace `error-04`

> [!tip]
> Pour résoudre cette erreur, utilisez la commande `kubectl create` pour créer une [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/) `test` contenant le litéral `formation="Exercice terminé"`.
> ```shell
> kubectl create -n error-04 cm test --from-literal=formation="Exercice terminé"
> ```

## ERROR-05

Via `kubectl get pods -n error-05` vous observez des pods en erreur avec le status `Error`. Accédez aux évènements du Pod via `kubectl get events -n error-05` ou via la commande 

```shell
kubectl describe -n error-05 deployment kuard
```

Vous constaterez l'erreur `OOMKilled` et la mention `memory limit is too low`, indiquant que le kubelet n'arrive pas a démarrer le conteneur spécifié car il atteint, dès son démarrage, sa limite de mémoire et est directement `OOMKilled` par le système.
Vous constaterez également que la limite en mémoire pour les pods de ce déploiement (cd `.spec.template.spec.containers.resources.limits.memory`) est de `1Mi`.

**deployment.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kuard
spec:
  selector:
    matchLabels:
      app-of: error-05
  replicas: 2
  template:
    metadata:
      labels:
        app-of: error-05
    spec:
      containers:
        - image: gcr.io/kuar-demo/kuard-amd64:blue
          name: kuard
          ports:
          - containerPort: 8080
            name: http
            protocol: TCP
          resources:
            limits:
              cpu: 100m
              memory: 1Mi
```

> [!tip]
> Pour résoudre cette erreur, utilisez la commande `kubectl edit` sur l'object Déploiement afin de corriger la limit en memoire pour utilisé `128Mi`
> ```shell
> kubectl edit -n error-05 deploy what-a-service
> ```

