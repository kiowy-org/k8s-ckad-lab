# Network Policies

> [!note]
> [Documentation NetworkPolicies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

## Mise en place

Appliquez le fichier `netpol.yaml` au cluster, il va créer dans votre namespace une application contenant un pod `web-app`, un pod `mysql-db` et les services pour les exposer.

```bash
kubectl apply -f netpol.yaml
```

Le fichier `netpol.yaml` applique également au cluster dans votre namespace une [NetworkPolicy](https://kubernetes.io/docs/concepts/services-networking/network-policies/) qui bloque tout les trafics (entrant et sortant).

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-all
  labels:
    exam: netpol
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

Liste tous les objets et assurez-vous que leur état affiche `Ready`.

## Exercice

Le Pod exécutant l'application web expose le port de conteneur `3000` (vous pouvez y accéder via `port-forward` et un navigateur).
L'application web affiche `Successfully connected to database!` si elle arrive à communiquer avec la base, et `Failed to connect to database: <message d'erreur>` en cas d'erreur.

[À partir de la documentation](https://kubernetes.io/docs/concepts/services-networking/network-policies/), ajoutez une nouvelle `NetworkPolicy` permettant à l'application web de se connecter à `mysql-db` uniquement sur le port `3306` (pensez à ajouter le label `exam=netpol` à votre `NetworkPolicy`).

## Nettoyage

```bash
kubectl delete po,svc,netpol -l exam=netpol
```

> [!caution]
> Assurez vous qu'il n'y a plus de NetworkPolicies dans votre namespace, faute de quoi les communications entre les autres applications seront coupées.
