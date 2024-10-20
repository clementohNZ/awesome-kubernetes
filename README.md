# Awesome Kubernetes

## Contents
* [Possible Errors](#possible-errors)
* [Useful Kubernetes Commands](#useful-kubernetes-commands)
    * [Pod Management](#pod-management)
        * [Force delete a pod](#force-delete-a-pod)
        * [Delete pods that are evicted](#delete-pods-that-are-evicted)
* [Helpful Links](#useful-links)
* [Contribution](#contribution)

## Possible Errors
Common errors and the solutions.

### setenv: invalid argument
```
container init caused \"setenv: invalid argument\"": unknown
```
This can occur when you are trying to inject a secret that isn't base64 encoded. Double-check your secret file using `kubectl get secret <SECRET_NAME> --output=yaml -n <NAMESPACE>`. Make sure all the values are base64 encoded. If not, updated them
with the base64 encoded version and reapply the manifest.

To base64 encode something, you can use `echo <SOME_TEXT> | base64`

## Useful Kubernetes Commands
Commands that are useful for managing kubernetes setups. Explanations
of why these commands are useful based on real-world experiences included.

### Pod Management
#### Force delete a pod
Sometimes pods aren't fully deleted due to external errors e.g. image doesn't
exist (but used to exist)... so they still appear in your deployment and take up resources. You
would like to delete them, but you can't delete them very easily through your
management UI or programmatically.

`kubectl delete pod <PODNAME> --grace-period=0 --force --namespace <NAMESPACE>`

#### Delete pods that are evicted
Pods can be evicted from an instance for many reasons. Most commonly, this is due
to a starvation of resources. These can end up polluting your cluster because
they aren't automatically removed.

You can run the following command in a cron job to periodically sterilize your
cluster of pods of inactive evicted pods.

```sh
function deleteEvictedPods() {
    environments=(
        development
        staging
        production
        traefik
        kube-system
        kube-public
        kube-node-lease
        default
    )

    for environment in "${environments[@]}"
    do
        echo "==================================================================================="
        echo "==================================================================================="
        echo "Removing evicted pods for namespace: \"${environment}\""
        echo "---------------------------------------------------"

        for each in $(kubectl get pods -n ${environment} | grep Evicted | awk '{print $1}');
        do
            kubectl delete pods $each -n ${environment}
        done
        echo "==================================================================================="
        echo "==================================================================================="
    done
}
```

## Delete Everything in Namespace
```sh
kubectl delete all --all -n lube-backend-api-primary
```

## Contribution
When submitting your helpful command, it would be doubly helpful to include some context
on why the command is useful.

## Useful Links
- [Kubectl Aliases](https://github.com/ahmetb/kubectl-aliases) - This will
add shortcuts for Kubernetes and make your development faster.
