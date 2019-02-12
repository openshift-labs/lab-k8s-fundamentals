---
Title: Creating the Resources
PrevPage: 03-deploying-an-application
---

By doing a dry run deployment, you have seen the resources that will be created from the `database-all.yaml` and `frontend-all.yaml` files which describe our application. You will note that for each application component, there isn't just one resource definition, but a number for each.

To actually deploy the database component, now run:

```execute
kubectl apply -f database-all.yaml
```

As with the dry run, `kubectl apply` will list the resources, except this time the resources will be created.

```
secret/blog-credentials created
service/blog-db created
persistentvolumeclaim/blog-database created
deployment.extensions/blog-db created
```

The key resource in this list is `deployment.extensions/blog-db`. This is what is called a _Deployment_ resource. It specifies the name of the container image to be deployed for an application, how many instances should be started, and the strategy for how the deployment should be managed.

To monitor progress of the deployment, and know when it has completed, you can run the command:

```execute
kubectl rollout status deployment.extensions/blog-db
```

The argument is the full name of the resource, including the type of resource and the name for this instance. In this case the instance was called `blog-db`.

Similarly, to deploy the frontend component, run:

```execute
kubectl apply -f frontend-all.yaml
```

This should output:

```
configmap/blog-settings created
persistentvolumeclaim/blog-media created
deployment.extensions/blog created
service/blog created
ingress.extensions/blog created
```

Run:

```execute
kubectl rollout status deployment.extensions/blog
```

to monitor and wait for it to be deployed.

In the resources created for the front end web application, the `ingress.extensions/blog` is special, in that it is what sets up access to our web application using a public URL.

You can get a summary description of the resource by running:

```execute
kubectl get ingress.extensions/blog
```

This should display output similar to:

```
NAME  HOSTS                                        ADDRESS  PORTS  AGE
blog  blog-%project_namespace%.%cluster_subdomain%  80       1m
```

The host name shown is that by which the web application can be accessed. Access it now at:

http://blog-%project_namespace%.%cluster_subdomain%
