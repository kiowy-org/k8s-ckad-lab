# Debug ( the fun part )

Le clusters c'est rempli de erreurs et de bug dans déploiement des applications ! 😱

Dans chaque namespace `error-*` se trouve une erreur identifier là et effectuer les actions nécéssaires à sa resolution.

## Commandes qui pourront vous aider à avoir de l'information

```shell
# Afficher les logs d'un pod
kubectl logs pod <pod_name> -n <namespace>
kubectl logs pod <pod_name> -n <namespace> -f
# Accéder aux évènements et statuts d'une ressources
kubectl describe <resource_kind> <resource_name>
# Afficher les évènements Kubernetes d'un namespace
kubectl get events
kubectl get events -n <namespace>
# Afficher les pods qui consomme le plus de ressources
kubectl top pod
kubectl top pod -A 
kubectl top pod -A --sort-by=cpu
# Afficher les nodes qui consomme le plus de leur ressources
kubectl top node
```
