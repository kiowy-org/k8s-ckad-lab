# 3. Services

Dans cette section, nous allons découvrir les services, et les readiness probes.

Appliquez le manisfest suivant au cluster.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: kuard
    ver: "1"
    env: prod
  name: kuard
spec:
  replicas: 3
  selector:
    matchLabels:
      app: kuard
      ver: "1"
      env: prod
  strategy: {}
  template:
    metadata:
      labels:
        app: kuard
        ver: "1"
        env: prod
    spec:
      containers:
      - image: gcr.io/kuar-demo/kuard-amd64:blue
        name: kuard-amd64
        ports:
        - name: http
          containerPort: 8080
          protocol: TCP
```

Ce fichier contient la définition d'un déploiement, que nous verrons dans la section suivante. Il permet de gérer plusieurs pods identiques.

<details>
    <summary>Aide</summary>

```shell
vim ./exercices/kuard-deploy.yaml                 # Copiez le manifest dans ce fichier
kubectl apply -f ./exercices/kuard-deploy.yaml
```

</details>

Afin d'exposer ces pods via le réseau, nous allons créer un service. Utilisez la commande `kubectl expose deployment kuard` afin que kubectl créé automatiquement un service.

Vérifiez que le service est bien créé grâce à la commande `kubectl get svc -o wide`.

Utilisez la commande `port-forward` vue précedemment afin d'accéder à kuard sur http://localhost:8080 . Dans l'onglet DNS, vérifiez le nom de domaine `kuard`.

Que constatez vous ?

<details>
    <summary>Aide</summary>

```shell
# Il est possible de faire un port-forward vers un service
kubectl port-forward svc/kuard 8080:8080
```

</details>

## Readiness probe

Le mécanisme de readiness probe permet d'indiquer à K8S quand envoyer du traffic vers le pod.

Ajoutez une readiness probe au pod kuard en éditant le déploiement avec la commande `kubectl edit deployment kuard`.

Ajoutez le code suivant (au même niveau que l'entrée `image`).

```yaml
readinessProbe:
    httpGet:
        path: /ready
        port: 8080
    periodSeconds: 2
    initialDelaySeconds: 0
    failureThreshold: 3
    successThreshold: 1
```

Validez la modification (dans vi : `Esc` + `:wq`)

Rendez vous sur le pod kuard, dans l'onglet readiness, changez le code de retour de la readiness probe et vérifiez le comportement sur kubernetes.

(Vous pouvez observer les pods présents dans le load balancing du service via la commande `kubectl get endpoints kuard -w`).

## NodePort

Pour accéder aux pods depuis l'extérieur, on peut utiliser le service de type `NodePort`.

Utilisez la commande `kubectl edit svc kuard` afin de modifier le service. Éditez le champ `spec.type` en indiquant la valeur `NodePort`.

Utilisez ensuite la commande `kubectl get svc kuard` afin d'obtenir le numéro de port ouvert sur les noeuds, et la commande `kubectl get nodes -o wide` afin d'obtenir l'IP publique d'un des noeuds du cluster.

Tentez ensuite d'accéder à http://\<node-ip\>:\<nodeport-port\> , rafraichissez la page pour vérifier que le load balancing fonctionne toujours.

## LoadBalancer

Modifiez de nouveau le service, en indiquant `LoadBalancer` dans le champ `spec.type`. Attendez quelques minutes, puis observez le résultat de la commande `kubectl get svc kuard`, que constatez vous ? Tentez d'accéder à kuard grâce aux informations obtenues.

Section suivante, [les controllers](4_controllers.md)

## Ingress

Complétez la règle d'ingress suivante en ajoutant le chemin `/k8s-training` pour qu'il renvoit sur le port `8080` du service `web-app-service`.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-wildcard-host
spec:
  rules:
  - host: "foo.bar.com"
    http:
      paths:
      - pathType: Prefix
        path: "/bar"
        backend:
          service:
            name: service1
            port:
              number: 80
```

## NetworkPolicy

[Ça ce passe ici. 👈](./network_policies)
