# 2. Labels

## Exercice 1 : Premiers labels

Ajoutez un label `canary=true` à votre pod kuard. 
Utilisez l'option `-l` de `kubectl get` pour vérifier que votre label permet bien de trier les pods.

<details>
    <summary>Aide</summary>

```bash
kubectl get pods --show-labels # Affiche les labels sur une liste
kubectl label pods kuard "canary=true" 

kubectl get pods -L canary
kubectl get pods -l canary=false

kubectl label pods kuard "canary-" # Retire un label
```

</details>

## Exercice 2 : Labels aerobics

***[ TODO ]***

Vous pouvez également annoter votre pod, avec `kubectl annotate pod kuard k8s-training/owner=<prenom>`. Vous pouvez vérifier votre annotation avec `kubectl describe`.

Section suivante, [les services](3_services.md)
