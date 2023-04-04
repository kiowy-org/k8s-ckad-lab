# 5. ConfigMaps et Secrets

## Exercice 1

Commencez par créer un configmap incluant un fichier de config, et des paramètres (objectif: une seule commande 😉).

<details>
<summary>Solution</summary>

```bash
kubectl create configmap my-config \
--from-file=./configmaps_et_secrets/simple_config.txt \
--from-literal=extra-param=extra-value \
--from-literal=another-param=another-value
```
</details>

Inspectez ensuite ce configmap afin d'en afficher les valeurs

```bash
kubectl get cm
kubectl describe cm my-config
kubectl get cm my-config -o yaml
```

Enfin, créez un pod qui utilise ce configmap.

<details>
<summary>Aide</summary>

```bash
kubectl apply -f ./configmaps_et_secrets/2_kuard_config.yaml
```
</details>

Vérifiez dans kuard les valeurs présentes dans l'environnement et sur le filesystem.

## Exercice 2

Créez un secret contenant un certificat, et montez le dans kuard.

Commencez par récupérer les fichiers de certificat.

```bash
curl -o kuard.crt https://storage.googleapis.com/kuar-demo/kuard.crt
curl -o kuard.key https://storage.googleapis.com/kuar-demo/kuard.key
```

Puis, utilisez kubectl pour créer un secret à partir des fichiers.

```bash
kubectl create secret generic kuard-tls \
--from-file=kuard.crt \
--from-file=kuard.key
```

Affichez les données du secret, que constatez vous ?

<details>
<summary>Aide</summary>

```bash
kubectl get secret
kubectl describe secret
```

</details>

Créez un Pod utilisant ce secret, vérifiez le résultat dans kuard

```bash
kubectl apply -f ./configmaps_et_secrets/kuard-secret.yaml
```

Donner la valeur en clair du champs `kuard.key` (objectif: une seule commande (encore) 😉)

<details>
<summary>Solution</summary>

```bash
kubectl get secret kuard-tls -o jsonpath='{.data.kuard\.key}' | base64 -d
```

</details>

Section suivante, [les RBAC](6_rbac.md)
