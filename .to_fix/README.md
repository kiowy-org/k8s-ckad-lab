# Environnement à débugger

install local :

```sh
alias k=kubectl
k apply -f .to_fix/namespaces.yaml
k apply -k .to_fix/error-01 -n error-01
k apply -k .to_fix/error-02 -n error-02
k apply -k .to_fix/error-03 -n error-03
k apply -k .to_fix/error-04 -n error-04
k apply -k .to_fix/error-05 -n error-05
```

Les environnements `error-*` sont en erreur à propos. Ils sont conçus pour être résolu impérativement via la commande `kubectl`. Merci de ne pas corriger les fichiers de ce dossier.

> Ce dossier est destiné au formatteur pour la mise en place des environnement via ArgoCD.

## ArgoCD

Le déploiement des éléments d'infrastructures des environnements est automatisé via ArgoCD.

### Prerequis

Installer ArgoCD dans le cluster de formation :

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Deployer les environnements

Pour déployer tous les environnements, exécutez la commande :

```sh
kubectl apply -f argocd-applications.yml
```

### Accéder au dashboard d'ArgoCD

Utilisez le port-forward sur le service `argocd-server` du namespace `argod` port `8080` vers `443`:

```sh
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Accédez via un navigateur à [https://localhost:8080](https://localhost:8080) et connectez-vous avec l'identifiant `admin` et le mot de passe que vous pourrez récupérer via la commande :

```sh
kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d
```
