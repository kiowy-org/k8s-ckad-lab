# 1. Les Pods

Dans cette sections, nous allons manipuler le composant de base de Kubernetes : le Pod.

## Exercice 1

Créez un pod appelé `kuard` grâce à `kubectl run`.

<details><summary>Réponse</summary>
  
  ```bash
    kubectl run kuard --image=gcr.io/kuar-demo/kuard-amd64:blue
    kubectl get pods
    kubectl delete pod/kuard
  ```

</details>
  
## Exercice 2

Créez un pod kuard dans un fichier (ex: `./exercices/kuard-pod.yaml`).

<details><summary>Validation</summary>
  
  ```bash
    # Si vous obtenez une erreur, vous avez peut être oublié de supprimer le pod kuard de l'exercice 1...
    kubectl apply -f ./exercices/kuard-pod.yaml
  ```

</details>

Essayez les commandes `kubectl` qui permettent de manipuler les pods.

```shell
kubectl get pods # Pour lister les pods
kubectl describe pod kuard # Affiche les informations détaillées du pod
```

Dans un autre terminal, exécutez la commande `kubectl port-forward pod/kuard 8080:8080` puis accédez à http://localhost:8080 (laissez bien la commande `port-forward` en cours d'exécution).

Vous pouvez ensuite tuer la commande (`Ctrl+C`), puis executez les commandes suivantes :

```shell
kubectl exec kuard -- date
kubectl exec -it kuard -- ash
```

Que déduisez vous le l'utilisation des options `i` et `t` ?

Enfin, visualisez les logs de vontre conteneur grâce à `kubectl logs kuard`.
Quelques options utiles :

* `-c` Vous permet de sélectionner le conteneur dans un pod multi-conteneurs.
* `--previous` vous permet de voir les logs du précedent contenur (dans le cas d'un conteneur qui a été redémarré).

Pour finir, supprimez le pod depuis son fichier grâce à la commande `kubectl delete -f ./exercices/kuard-pod.yaml`.

## Pod kuard complet

À l'aide du fichier `utils/pod_template.yaml`, regroupez toutes les spécifications abordées pour créer un pod `kuard` ayant:

* Le nom `kuard`
* L\'image `gcr.io/kuar-demo/kuard-amd64:blue`
* Une sonde de vie en `/healthy`
* Une sonde de readiness en `/ready`
* Le port `8080` ouvert en `TCP`
* Le container principal qui accède au volume `kuard-data` via le dossier `/data`
* Un volume `kuard-data` sur le server `my.nfs.server.local`
* Demande `100m` de puissance de calcul et `128Mio` de mémoire.
* Ne dépasse pas `250m` de puissance de calcul et `256Mio` de mémoire.

## Multiple container pod

En vous aidant du template du kuard, créez un pod associé à un volume vous permettant l'accès au dossier `/tmpfs`.

Ajouter à votre pod un container d'initialisation pour créer un fichier dans le dossier susmentionner en executant la commande:

```shell
echo 'Hello there !' > /tmpfs/data.txt
```

Utilisez ce manifest pour créer une instance de ce pod dans votre namespace et connecter-vous à votre pod pour accéder au contenu du fichier `/tmpfs/data.txt`.

## Kuard deploy

Créez un déploiement d'un pod busybox à 3 replicas ouverts sur le port `8080` et les labels `app`, `ver`, et `env` respectivement à `busy`, `2` et `dev` et enregistrez le dans un fichier.

<details>
    <summary>On peut aller plus vite 🤫</summary>

```bash
kubectl create deployment --replicas=2 --image=busybox --port=8080 --labels='app=busy,ver=2,env=dev' --dry-run=client -o yaml > deployment_kuard.yaml
```

</details><br/>

Section suivante, [les labels](2_labels.md).
