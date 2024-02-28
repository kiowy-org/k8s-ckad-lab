# 7. Statefulsets

## Exercice 1

Créez un Statefulset MongoDB.

Appliquez le fichier `./stateful_set/1-mongo-simple.yaml` pour créer l'objet StatefulSet.
Appliquez ensuite le fichier `./stateful_set/2-mongo-service.yaml` afin de créer le headless service.

> [!NOTE]
> Un [Headless service](https://kubernetes.io/fr/docs/concepts/services-networking/service/#headless-services) est un service sans Cluster IP.
> 
> Particulièrement utile pour s'interfacer d'autre système de découverte de pair, le headless service n'est pas géré par le `kube-proxy`.
> Aucun `LoadBalancing` ou proxy n'est effectué par Kubernetes pour ces services.

Vérifiez que les pods démarrent bien dans l'ordre. 

Instanciez un pod kuard (`kubectl run kuard --image=gcr.io/kuar-demo/kuard-amd64:blue`), faites un `port-forward` et tentez une résolution DNS de :

* `mongo`
* `mongo-1.mongo`

Que constatez vous ?

Pour initialiser mongoDB utilisez les commandes suivantes :

```bash
kubectl exec -it mongo-0 -- mongo
# Dans le conteneur
rs.initiate({_id: "rs0", members:[{_id: 0, host: "mongo-0.mongo:27017"}]});
rs.add("mongo-1.mongo:27017");
rs.add("mongo-2.mongo:27017");
```

Que se passe t'il si on scale notre StatefulSet ?

## Exercice 2

Nous allons corriger notre statefulset, afin qu'il supporte le scaling.

Supprimez d'abord votre StatefulSet de l'exercice 13.

Appliquez les fichiers `./stateful_set/3-mongo-configmap.yaml` et `./stateful_set/4-mongo.yaml`.

Vérifiez que votre StatefulSet a bien démarrez. Nous allons ensuite charger des données :

```bash
curl -O https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/primer-dataset.json

cat primer-dataset.json | kubectl exec -it mongo-0 -- mongoimport --db test --collection restaurants --drop
```

Enfin, testez que vous pouvez accéder aux données :

```bash
kubectl exec -it mongo-0 -- mongo test --eval "db.restaurants.find()"
```

Section suivante, [Debug](8_debug.md)
