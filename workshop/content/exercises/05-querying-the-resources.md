---
Title: Querying the Resources
PrevPage: 04-creating-the-resources
NextPage: 06-labelling-of-resources
---

The web console for a Kubernetes cluster can be used to display a visual representation, in your browser, of what resources have been created and the relationship between them, but most developers will interact with a Kubernetes cluster from the command line using `kubectl`.

To see a list of all the deployments in the current namespace which have already been created, run:

```execute
kubectl get deployment
```

This should yield output:

```
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
blog      2         2         2            2           5m
blog-db   1         1         1            1           5m
```

To narrow in on a specific resource, the name of that resource can be added to the command:

```execute
kubectl get deployment/blog
```

This should then yield output for just the one resource.

```
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
blog      2         2         2            2           5m
```

The output format for the information displayed can be overridden. In many cases, a resource type can be made to display additional information in the table by using the `-o wide` option.

```execute
kubectl get deployment/blog -o wide
```

In the case of a deployment, this results in details of the containers that would be created from the deployment, the names of the images run to create the containers, and the set of labels applied to the instances of the application created from the deployment.

```
NAME  DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE  CONTAINERS  IMAGES                                   SELECTOR
blog  2        2        2           2          5m   frontend    openshiftkatacoda/blog-django-py:latest  app=blog
```

Labels in Kubernetes are very important and are used in various ways to create links between resources to indicate they are related, or part of the same application.

To see much more detailed information about a resource `kubectl describe` can be used.

```execute
kubectl describe deployment/blog
```

For a deployment, just the start of what you should see is:

```
Name:                   blog
Namespace:              %project_namespace%
CreationTimestamp:      Tue, 19 Feb 2019 00:52:23 +0000
Labels:                 app=blog
Selector:               app=blog
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
    ....
```

This is still in a semi human readable form and isn't suitable for machine processing. To instead see the raw resource definition, you can use the `-o yaml` display output option to `kubectl get`.

```execute
kubectl get deployment/blog -o yaml
```

Or if you prefer to work with JSON rather than YAML, you can use:

```execute
kubectl get deployment/blog -o json
```

If you were only interested in specific information from the resource, you can also provide a template for what information would be displayed.

To list the names of the containers created from a deployment, and the images run for each container, you could run:

```execute
kubectl get deployment/blog -o template --template '{{range .spec.template.spec.containers}}{{.name}} {{.image}}{{"\n"}}{{end}}'
```

This should yield:

```
frontend openshiftkatacoda/blog-django-py:latest
```

When using a template to extract details from the resource, or in any case where you are trying to interpret a raw resource definition, you will need to know what the structure of the resource is.

Information on the structure of a resource can be found in the Kubernetes documentation, but a more convenient method of accessing details about the structure of a resource is the `kubectl explain` command.

```execute
kubectl explain deployment
```

For a deployment resource, this will display:

```
KIND:     Deployment
VERSION:  extensions/v1beta1

DESCRIPTION:
     DEPRECATED - This group version of Deployment is deprecated by
     apps/v1beta2/Deployment. See the release notes for more information.
     Deployment enables declarative updates for Pods and ReplicaSets.

FIELDS:
   apiVersion   <string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#resources

   kind <string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/api-conventions.md#types-kinds

   metadata     <Object>
     Standard object metadata.

   spec <Object>
     Specification of the desired behavior of the Deployment.

   status       <Object>
     Most recently observed status of the Deployment.
```

Only the top level attributes are displayed, but you can drill down into any attribute which is an object, by supplying a dot separated path for the attribute you are interested in.

```execute
kubectl explain deployment.spec.template.spec.containers
```

To see a full list of the resource types available in a Kubernetes cluster, along with any aliases for specific types, you can run:

```execute
kubectl api-resources
```

One special alias that exists which isn't in this list is `all`. This maps to a list of the core Kubernetes resource objects.

```execute
kubectl get all -o name
```

The `-o name` output format in this case lists just the names. For the deployments made so far, this should yield output similar to:

```
pod/blog-c4fb96fd9-4g5k2
pod/blog-c4fb96fd9-8w7sd
pod/blog-db-d75f8997-twxnn
service/blog
service/blog-db
deployment.apps/blog
deployment.apps/blog-db
replicaset.apps/blog-c4fb96fd9
replicaset.apps/blog-db-d75f8997
route.route.openshift.io/blog-96s5l
```

You may have noticed that the `ingress` for the front end web application doesn't appear. This is because it isn't categorised as being one of the core resource objects.

Where you want to query on multiple resource object types, rather than use `all`, which often isn't all of what you want, you can list all the resource type names separated by a comma.

```execute
kubectl get deployment,service,ingress,secret,configmap,pvc -o name
```

This would yield:

```
deployment.apps/blog
deployment.apps/blog-db
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
