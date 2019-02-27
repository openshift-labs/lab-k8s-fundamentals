---
Title: Deploying an Application
PrevPage: 02-accessing-the-cluster
NextPage: 04-creating-the-resources
---

Now you have checked your access to the Kubernetes cluster is working, we are going to immediately jump in and deploy a complete application, consisting of a front end web application implementing a blog site, along with a PostgreSQL database for storing the blog posts.

This is to show you how quickly you can deploy a complete application to Kubernetes if you already have the configuration. Once the complete application has been deployed, we will start to pull the application and the configuration apart, and deploy parts of it over again in smaller steps, so you can understand how it fits together and how it uses Kubernetes.

The first part of the application we want to deploy is the PostgreSQL database. The set of resources for deploying this can be found in the `application/database.yaml` file.

If you want to have a scan through the contents of this file, run:

```execute
cat application/database.yaml
```

For this workshop we will be using YAML format files. Kubernetes will also accept JSON format files.

If you look closely you will see that the file consists of a list of separate resource definitions. Rather than try and parse out what each of these are at this point, you can have Kubernetes tell you what resources it would create when the file is processed. To do this, run:

```execute
kubectl apply -f application/database.yaml --dry-run
```

This should output:

```
secret/blog-credentials created (dry run)
service/blog-db created (dry run)
persistentvolumeclaim/blog-database created (dry run)
deployment.apps/blog-db created (dry run)
```

The `kubectl apply` command in this case is what is used to create resources from a configuration file. We however used the `--dry-run` option, which says to tell us what would be created, but don't actually do it. The `--dry-run` option is useful because it will tell you what resources would be created, but also validates the resource definitions and will warn you if you have errors in them.

If you have read any examples about using Kubernetes previously, you may also have seen the `kubectl create` command used in a very similar way. There are actually three commands that appear similar, and it can be confusing.

The three commands are:

* `kubectl create` - Creates a set of resources from a file. If any resource of the same name exists, it will not be replaced and an error will be displayed.
* `kubectl apply` - Creates a set of resources from a file. If any resource of the same name exists, the new resource will be merged with the one which already exists.
* `kubectl replace` - Replace a set of resources from a file. If any of the resources do not already exist, an error will be displayed.

In most cases you can always safely use `kubectl apply`, with the result being that resources will be created if they don't exist, or replaced if they do. The other commands might be used if scripting application creation and you want the checks.

If you are ever uncertain about what a command does, or what options it accepts, you can run it with the `--help` option.

```execute
kubectl apply --help
```

Before actually deploying the database, let's also check out what resources would be created for the front end web application implementing the blog site.

The set of resources for deploying this can be found in the `application/frontend.yaml` file.

If you want to have a scan through the contents of this file, run:

```execute
cat application/frontend.yaml
```

To see what resources would be created from this file, run `kubectl apply` with the `--dry-run` option:

```execute
kubectl apply -f application/frontend.yaml --dry-run
```

This should output:

```
configmap/blog-settings created (dry run)
persistentvolumeclaim/blog-media created (dry run)
deployment.apps/blog created (dry run)
service/blog created (dry run)
ingress.extensions/blog created (dry run)
```
