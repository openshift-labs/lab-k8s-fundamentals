---
Title: Labelling of Resources
PrevPage: 05-querying-the-resources
---

The ability to use `kubectl` to query resource definitions and their current status is important because it is the resource definitions which drive Kubernetes and what it does.

As we already saw, it isn't a single resource definition which defines everything about the deployment of an application, but a set of resources.

In order to indicate that a set of resources are related, they can be labelled. You can then perform queries to look up resources based on the labels.

The last query you ran was:

```execute
kubectl get deployment,service,ingress,secret,configmap,pvc -o name
```

This yielded the output:

```
deployment.extensions/blog
deployment.extensions/blog-db
service/blog
service/blog-db
ingress.extensions/blog
secret/blog-credentials
secret/builder-dockercfg-xcscw
secret/builder-token-hckgq
secret/builder-token-nnq8t
secret/default-dockercfg-9vjlk
secret/default-token-jvb28
secret/default-token-r27cq
secret/deployer-dockercfg-glzl5
secret/deployer-token-2ws8c
secret/deployer-token-rfsh5
configmap/blog-settings
persistentvolumeclaim/blog-database
persistentvolumeclaim/blog-media
```

This includes all resources from the current project of the listed types. The list therefore included resources for the front end web application, the database, as well as special resources related to service accounts that have been pre-created in the namespace you are working in.

In order to narrow the results down so it shows just the resources for the front end web application, we can add to the `kubectl get` command a label selector using the `-l` or `--selector` option.

```execute
kubectl get deployment,service,ingress,secret,configmap,pvc -o name -l app=blog
```

This should yield:

```
deployment.extensions/blog
service/blog
ingress.extensions/blog
configmap/blog-settings
persistentvolumeclaim/blog-media
```

The format of the value passed to the option is `label-name=label-value`.

For the sample application you are deploying, all resources for the front end web application were given a label of `app=blog`.

In the case of the database, they were all given a label of `app=blog-db`. For the database, running:

```execute
kubectl get deployment,service,ingress,secret,configmap,pvc -o name -l app=blog-db
```

should yield the separate resources:

```
deployment.extensions/blog-db
service/blog-db
secret/blog-credentials
persistentvolumeclaim/blog-database
```

One important use case for labels is when you want to delete an application. Where an application is defined by a set of resources, it is cumbersome and error prone to have to delete each resource one at a time. Using a label selector, you can delete them all at once.

The procedure for deleting an application would be to first use `kubectl get` with a label selector to determine that you have the correct set of resources, and then substitute `kubectl get` with the `kubectl delete` command.

Delete the front end web application now by running:

```execute
kubectl delete deployment,service,ingress,secret,configmap,pvc -l app=blog
```

This should output:

```
deployment.extensions "blog" deleted
service "blog" deleted
ingress.extensions "blog" deleted
configmap "blog-settings" deleted
persistentvolumeclaim "blog-media" deleted
```

Having deleted the front end web application, we will now re-deploy it, but this time we will do it one step at a time so you can understand what each of the resources is for and what Kubernetes does in response to the resource definitions being created.
