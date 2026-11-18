# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom : ABUR Kubra**
**Groupe : RS2**

---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   >La commande affiche 3 noeux : 
   >k3d-lab-agent-0 (worker), k3d-lab-agent-1 (worker) et k3d-lab-server-0 (control-plane)
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   >docker ps montre les containers en cours d'execution, on voit que les 3 noeux dans le get nodes sont dans le docker ps, donc chaque noeud Kub est un conteneur

## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   >La commande crée un Deployment, qui crée automatiquement un ReplicaSet et un Pod contenant le conteneur Nginx
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   >Après la suppression du Pod, Kubernetes en a automatiquement créé un nouveau. C'est parce que le Deployment souhaite toujours avoir un Pod en fonctionnement

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   >Non, car le nouveau Pod a été recréé à partir de l'image Nginx d'origine. La modification faite dans l'ancien Pod n'a pas été conservée donc on a l'ancienne page avec "Welcome to nginx!"
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   >Parce que le port-forward était connecté à l'ancien Pod. Lorsqu'il a été supprimé, la connexion a été interrompue. Il a fallu relancer le port-forward pour accéder au nouveau Pod

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   >kubectl create sert à créer une ressource. kubectl apply permet de créer ou de mettre à jour une ressource à partir d'un fichier YAML
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   >Le selector permet au Service de trouver les Pods auxquels envoyer le trafic grâce à leurs labels. S'il correspond à aucun Pod, le Service trouve aucune cible et le site devient inaccessible via ce Service.

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   ```
@KubraGu ➜ /workspaces/atelier-kubernetes (main) $ kubectl get pods -l app=site -o wide
NAME                    READY   STATUS    RESTARTS   AGE     IP          NODE               NOMINATED NODE   READINESS GATES
site-68f8f45b86-279zf   1/1     Running   0          10s     10.42.2.5   k3d-lab-agent-1    <none>           <none>
site-68f8f45b86-cr5j5   1/1     Running   0          4m35s   10.42.1.8   k3d-lab-agent-0    <none>           <none>
site-68f8f45b86-v7rh9   1/1     Running   0          10s     10.42.0.9   k3d-lab-server-0   <none>           <none>
   ```
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    >site-d474c8465-hxrzc    0/1     ErrImagePull   0          4s
    >Warning  Failed     7s (x3 over 47s)   kubelet            spec.containers{nginx}: Failed to pull image "nginx:9.99": rpc error: code = NotFound desc = failed to pull and unpack image "docker.io/library/nginx:9.99": failed to resolve reference "docker.io/library/nginx:9.99": docker.io/library/nginx:9.99: not found
  Warning  Failed     7s (x3 over 47s)   kubelet            spec.containers{nginx}: Error: ErrImagePull
    >Le nouveau Pod avait le statut ErrImagePull, puis ImagePullBackOff. L'image nginx:9.99 existe pas et peut pas être téléchargée. La commande kubectl describe pod permet de trouver la cause dans la section Events
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    >Oui, le site reste accessible pendant la panne, car les trois anciens Pods fonctionnent toujours. Le nouveau Pod démarre pas à cause de l'image incorrecte, mais les anciens continuent de recevoir les requêtes

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    >https://probable-xylophone-w6rw6qrj45rhjg7-8585.app.github.dev/
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
    >Portainer utilise une interface graphique, alors qu'en ligne de commande, on utilise des commandes kubectl

## Bonus (facultatif)

14. Bonus réalisés et remarques :
    >

---

**Score obtenu avec `./scripts/check.sh` :** ... / ...
