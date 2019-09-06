# Useful Kubernetes Commands
Commands that are useful for managing kubernetes setups. Explanations
of why these commands are useful based on real-world experiences included.

## Contents
* [Pod Management](#pod-management)
    * [Force delete a pod](#force-delete-a-pod)
    * [Delete pods that are evicted](#delete-pods-that-are-evicted)
* [Helpful Links](#useful-links)
* [Contribution](#contribution)

## Pod Management
### Force delete a pod
Sometimes pods aren't fully deleted due to external errors e.g. image doesn't
exist (but used to exist)... so they still appear in your deployment and take up resources. You
would like to delete them, but you can't delete them very easily through your
management UI or programmatically.

`kubectl delete pod <PODNAME> --grace-period=0 --force --namespace <NAMESPACE>`

### Delete pods that are evicted
Pods can be evicted from an instance for many reasons. Most commonly, this is due
to a starvation of resources. These can end up polluting your cluster because
they aren't automatically removed.

You can run the following command in a cron job to periodically sterilize your
cluster of pods of inactive evicted pods.

`kubectl get po --all-namespaces --field-selector 'status.phase!=Running' -o json | kubectl delete -f -`

## Contribution
When submitting your helpful command, it would be doubly helpful to include some context
on why the command is useful.

## Useful Links
- [Kubectl Aliases](https://github.com/ahmetb/kubectl-aliases) - This will
add shortcuts for Kubernetes and make your development faster.
