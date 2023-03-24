# Debug ( the fun part )

Le clusters c'est rempli de erreurs et de bug dans déploiement des applications ! 😱

Dans chaque namespace `error-*` se trouve une erreur identifier là et effectuer les actions nécéssaires à sa resolution.

## Commande qui pourront vous aider

```shell
kubectl top pod
kubectl top pod -A 
kubectl top pod -A --sort-by=cpu
```

```shell
kubectl top node
kubectl top node -l 
kubectl top node --sort-by=cpu
```

```shell
## CMD
```
