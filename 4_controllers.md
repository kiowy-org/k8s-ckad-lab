## 4. Controllers

### Exercice 1

Créer un déploiement afin de gérer un ensemble de pods identiques.

<details>
    <summary>Aide</summary>

```shell
k create deployment kuard --image=gcr.io/kuar-demo/kuard-amd64:blue
```

</details>

Explorez son fonctionnement via les commandes suivantes :

```shell
kubectl describe rs kuard 

kubectl scale replicaset kuard --replicas=4 # Vous pouvez ensuite lister les pods pour vérifier que ça fonctionne

kubectl autoscale rs kuard --min=2 --max=5 --cpu-percent=80
kubectl get hpa # Vous pouvez également describe cet objet

kubectl delete rs kuard
```

### Exercice 2

Créez un déploiement grâce a un fichier et inspectez l'objet créé.

<details>
    <summary>Aide</summary>

```bash
kubectl create deployment kuard --image=gcr.io/kuar-demo/kuard-amd64:blue --replicas=3 --dry-run=client -o yaml > deploy.yaml
vim deploy.yaml
kubectl apply -f deploy.yaml
kubectl get deploy -o wide
kubectl describe deploy kuard
```

</details>

Afin de tester la fonctionalité de Rolling Update, nous allons provoquer un rollout via la commande suivante :

```bash
kubectl set image deploy kuard kuard-amd64=gcr.io/kuar-demo/kuard-amd64:green
```

Observez via `kubectl get pods -w` le rollout en cours.

Continuez avec les commande suivante afin de voir ce qui se passe en cas d'erreur :

```bash
kubectl get rs

kubectl set image deploy kuard kuard-amd64=gcr.io/kuar-demo/kuard-amd64:red
kubectl rollout status deploy kuard
kubectl rollout history deploy kuard
kubectl rollout undo deploy kuard

kubectl rollout pause deploy kuard
kubectl set image deploy kuard kuard-amd64=gcr.io/kuar-demo/kuard-amd64:purple
kubectl rollout history deploy kuard
kubectl rollout resume deploy kuard
```

### Exercice 3

Créez un job via le fichier dans le fichier `job-oneshot.yaml`, completer le avec les informations pour executer la commande `curl` sur `https://wttr.in/Moon` puis appliquer le au cluster.

Inspectez votre job et vos pods (`describe`, `get` et `logs`).

### Exercice 4

Faites un peu de ménage (via `kubectl delete -f`). Appliquez ensuite le fichier `./utils/example_job-parallel.yaml`.
Utilisez ensuite la commande `kubectl get pods -w`, observez le comportement des pods.

Nous allons ensuite créer des jobs, alimentés par une queue de travail (présente dans un pod kuard)

### Exercice 5

```bash
kubectl apply -f ./utils/example_jobs-1-queue.yaml

QUEUE_POD=$(kubectl get pods -l app=work-queue,component=queue \
-o jsonpath='{.items[0].metadata.name}')
kubectl port-forward pod/$QUEUE_POD 8080:8080 # Gardez cette commande active !

# Dans un autre terminal, exécutez :
./utils/example_jobs-2-load-queue.sh
curl localhost:8080/memq/server/stats

kubectl apply -f ./utils/example_jobs-3-consumer.yaml
```

Section suivante, [la configuration et les secrets](5_configmaps_et_secrets.md).
