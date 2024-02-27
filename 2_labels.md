# 2. Labels

## Exercice 1 : Premiers labels

Ajoutez un label `canary=true` à votre pod kuard. 
Utilisez l'option `-l` de `kubectl get` pour vérifier que votre label permet bien de trier les pods.

```bash
kubectl get pods --show-labels # Affiche les labels sur une liste
kubectl label pods kuard "canary=true" 

kubectl get pods -L canary
kubectl get pods -l canary=false

kubectl label pods kuard "canary-" # Retire un label
```

## Exercice 2 : Labels aerobics

Tout d'abord, créer 5 pods nginx nginx1...5 avec le labels `app=v1`.

<details>
    <summary>On peut aller plus vite 🤫</summary>

```bash
for i in `seq 1 5`; do kubectl run nginx$i --image=nginx -l app=v1 ; done
```

</details>
<br>

Effectuer ensuite les manipulations suivantes en utilisant une seule commande par instruction :

1. Afficher les labels des pods.
2. Ajouter le label `img=nginx` à tous les pods avec le label `app=v1`.
3. Changer le label `app` pour `app=v2` pour `nginx2` et `nginx3`.
4. Changer le label `app` pour `app=v3` pour `nginx4` et `nginx5`.
5. Afficher les différentes valeurs du label `app` de chaque pods dans une colonne dédié du `kubectl get` ( option `-L`).
6. Ajouter le label `version=old` aux pods avec les labels `app=v1` et `app=v2`.
7. Annoter les pods avec `k8s-training/owner=<prenom>`.
8. Supprimer le label `version` des pods avec le label `app=v2`.
9. Ajouter le label `prerelease=alpha` au pods `nginx4` et `nginx5`.
10. Retirer l'annotation `app=v2` du pod `nginx2`.
11. Afficher uniquement les pods `prerelease`, peux importe la valeur.
12. Afficher tous les pods et leurs labels séparés par colonne.

> [!TIP]
> Avant de changer de section, supprimer tous les pods créer durant cet exercice (en 2 commmandes maximum 😉).

Section suivante, [les services](3_services.md)

