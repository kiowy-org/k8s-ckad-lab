# 6. Utilisation des RBAC avec les ServiceAccount

Dans ce TP, nous allons explorer le fonctionnement des ServiceAccounts, Role, RoleBinding...

## 1. Mise en place

Nous allons démarrer quelques pods dans des namespaces, afin de comprendre le fonctionnement des autorisations entre namespaces. Créez un deploiement de `luksa/kubectl-proxy` dans votre namespace.

```shell
kubectl create deployment test --image=luksa/kubectl-proxy
```

Ouvrez deux terminaux, vous allez vous connecter à vos deux pods dans chacun d'eux.

Listez les pods dans vos namespaces (`kubectl get pods -n <ns>`).
Utilisez la commande `exec` pour ouvrir un shell dans vos pods.

```shell
kubectl exec -it <votre-pod> -n <ns> -- sh
```

## 2. Lister les services depuis vos pods

Depuis vos pods, essayez de lister les services du namespace :

```shell
curl localhost:8001/api/v1/namespaces/<prenom>/services
```

Quel retour obtenez vous de l'API ?

## 3. Autoriser le pod à lister les services

Afin que le pod dans votre namespace puisse lire les services de vote namespace, nous devons définir un rôle.

Adaptez le code suivant, afin d'autoriser la lecture d'un service, ainsi que lister les services.

Utilisez directement kubectl pour créer le rôle (n'oubliez pas de remplacer les ?) :

```shell
kubectl create role service-reader --verb=? --verb=? --resource=? -n bar
```

Afficher le manifest resultant de la commande.

Maintenant que le rôles est en place, vous devez le binder à un ServiceAccount. Utilisez la commande suivante pour attacher votre rôle au compte de service par défaut :

```shell
kubectl create rolebinding test --role=service-reader --serviceaccount=<prenom>:default -n <prenom>
```

Vous pouvez observer le détail du RoleBinding via `kubectl get rolebinding test -n <prenom> -o yaml`.

Maintenant, dans le pod de votre namespace, exécutez la commande suivante.

```shell
curl localhost:8001/api/v1/namespaces/<prenom>/services
```

<details>
    <summary>Execution direct 🤫</summary>

```bash
kubectl exec -it <votre-pod> -n <ns> -- sh curl localhost:8001/api/v1/namespaces/foo/services
```

</details><br/>

Quelle réponse obtenez vous ?

Enfin, utilisez la commande `kubectl edit rolebinding test -n <prenom>`.
Ajoutez un `subject` à votre RoleBinding

```yaml
subjects:
- kind: ServiceAccount
  name: default
  namespace: <autre_prenom>
```

Validez vos modifications.

Suite à ce binding, quelle seront les permissions du pod s'exécutant dans le namespace cet autre namespace ?

## 4. Utiliser les ClusterRoles

Afin d'accéder à une ressource ClusterWide, nous avons besoin de définir un ClusterRole. Exécutez la commande suivante afin de créer un ClusterRole permettant de lire les PersistentVolumes :

```shell
kubectl create clusterrole pv-reader --verb=get,list --resource=persistentvolumes
```

Depuis l'un de vos pods, tentez de lister les `PersistentVolumes` :

```shell
curl localhost:8001/api/v1/persistentvolumes
```

Nous allons autoriser le pod de votre namespace à lire les PersistentVolumes, utilisez un RoleBinding, généré directement via la commande `kubectl create rolebinding`.

Ré-essayez d'accéder aux PersistentVolumes depuis le pod, quel retour obtenez vous ?

Supprimez ensuite votre RoleBinding, et utilisez la commande `kubectl create clusterrolebinding` à la place.
Retentez de lister les PersistentVolumes, si tout va bien, ça devrait fonctionner cette fois !

## 5. Scope des ClusterRoles

Un ClusterRole peut s'appliquer sur des ressources ClusterWide, mais également sur des ressources Namespaced.
Le champ d'action dépendra du type de binding utilisé.

Affichez le contenu du ClusterRole view : `kubectl get ClusterRole view -o yaml`.
Vous voyez que ce rôle permet de voir tous les objets.

Dans le pod du namespace, exécutez les commandes suivantes :

```shell
curl localhost:8001/api/v1/pods
curl localhost:8001/api/v1/namespaces/<prenom>/pods 
```

Vous ne pouvez pas lister les pods ni au niveau du cluster, ni de votre namespace.

Ajoutez un ClusterRoleBinding : `kubectl create clusterrolebinding view-test --clusterrole=view --serviceaccount=<prenom>:default`

Essayez de lister les pods :

```shell
curl localhost:8001/api/v1/pods
curl localhost:8001/api/v1/namespaces/<prenom>/pods
curl localhost:8001/api/v1/namespaces/<autre_prenom>/pods 
```

Maintenant supprimez le ClusterRoleBinding, puis créez un RoleBinding dans le namespace:

```shell
kubectl create rolebinding view-test --clusterrole=view --serviceaccount=<prenom>:default -n <prenom>
```

Ré-executez les commandes suivantes :

```shell
curl localhost:8001/api/v1/pods
curl localhost:8001/api/v1/namespaces/<prenom>/pods
curl localhost:8001/api/v1/namespaces/<autre_prenom>/pods 
```

Section suivante, [les Statefulsets](7_statefulsets.md)
